---
title: (W8D4-5) Spring Data JPA, API 문서화
date: 2021-10-05 02:10:64
category: devcourse
draft: false
---

## Spring Data JPA

스프링에서 JPA를 편리하게 사용할 수 있도록 지원하는 프로젝트이다.

- 데이터 소스 및 엔티티 매니저 트랜잭션 매니저 설정을 자동으로 해준다.
- 데이터 저장계층에 대한 인터페이스를 지원하여 CRUD 작업을 편리하게 할 수 있다.
- CRUD 처리를 위한 공통 인터페이스 제공한다.
- repository 개발 시 인터페이스만 작성하면 실행 시점에 Spring Data JPA가 구현 객체를 동적으로 생성해서 주입한다.

JPA를 한 단계 추상화시킨 `Repository`라는 인터페이스를 제공함으로써 이루어진다.

JPA에서 사용자 정의 쿼리를 사용하는 방법

- Named Query
- [쿼리 메서드](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)
- @Query 어노테이션



## REST-API

Entity를 Transaction 밖으로 끌고 나가는 프로그래밍 습관은 좋지 않다. 예상치 못한 곳에서 쿼리가 나가는 문제가 있을 수 있다. - OSIV 패턴과 관련되어 있음

### Pageable 객체

페이징을 제공하는 중요한 인터페이스. JpaRepository를 사용할 때, findAll() 메서드에 Pageable 인터페이스를 파라미터로 전달하면 페이징을 사용할 수 있다.

PageRequest : 페이지 번호, 페이지당 데이터의 수, 정렬 방향. 찾을 페이지와 페이지의 size를 필수 인자로 받는다. 정적 팩토리 메서드로 생성한다.(of 메서드)

반환 타입에 따라서 각기 다른 결과를 제공한다.

- `Page<T>` 타입 : 게시판 형태의 페이징. count 쿼리를 포함하는 페이징으로, 카운트 쿼리가 자동으로 포함되어진다.
- `Slice<T>` 타입 : 더보기 형태의 페이징. 추가 count 쿼리 없이 다음 페이지 확인이 가능하다. 내부적으로 limit + 1 조회를 한다.
- `List<T>` 타입 : count 쿼리 없이 결과만 반환한다.

PageRequest를 컨트롤러에서 인자로 전달받는다. 응답은 Page로 전달한다. 클라이언트 입장에서 더 많은 정보를 얻을 수 있게 만들 수 있다.

```java
@GetMapping("/users")
public Page<User> getAllUsers(Pageable pageable) {
    return userRepository.findAll(pageable);
}
```

@ExceptionHandler -> 공통 에러 처리

RestController 테스트

- @Autowired MockMvc : 애플리케이션을 실행시키지 않고 테스트할 수 있게 하는 객체
  - mockMvc.perform()로 애플리케이션 end point에 요청해볼 수 있다.
  - @AutoConfigureMockMvc

- @Autowired ObjectMapper 역할
  - JSON -> Java object
  - Java object -> JSON

MockMvc를 이용하면 Restdocs를 통한 애플리케이션 문서화까지 가능하다.

### OSIV (Open EntityManager In View, Open Session In View)

- 영속성 컨텍스트를 뷰까지 열어두는 기능
- 영속성 컨텍스트가 유지되면 엔티티도 영속 상태로 유지된다. 뷰까지 영속성 컨텍스트가 살아있다면 뷰에서도 지연 로딩을 사용할 수가 있다.



## API 문서화 - Restdocs, Swagger

Spring REST Docs 는 RESTful 서비스의 문서화를 도와주는 도구이다. 기본적으로 [Asciidoctor](https://asciidoctor.org/) 를 사용하며, 이것을 사용해 HTML 을 생성한다.

문서와 API가 일치하지 않을 때 에러 발생 -> API 스펙과 실제 API 문서 스펙이 다르다는 것을 확인시켜준다. -> 문서의 최신화가 자동적으로 될 수 있게 해준다.

Restdocs 문서화 장점

- Contoller layer를 테스트할 수 있다.
- API가 정상 작동하는지 확인할 수 있다.
- 문서와 API 상태를 항상 일치시킬 수 있다.

Swagger 단점

- 문서화 코드와 운영 코드가 섞이게 된다. 유지보수 측면에서 좋지 않다. 
- 운영코드 해석이 어려워진다.
- API 검증이 자동적으로 따라오지 않는다.

| Spring REST Docs                                             | Swagger                                                      |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 코드의 추가 및 수정이 없다.                                  | 코드에 어노테이션 등을 추가해야 한다.                        |
| 테스트 코드 작성이 필요하며, 테스트 성공 시 문서가 생성된다. | 테스트 코드 없이 서비스 쪽 코드 및 어노테이션 추가로 문서를 생성할 수 있다. |
| 버전 변화에 유연하고 정확성이 높다.                          | 버전 변화에 맞춰 재작성해야 하며 이를 하지 않을 때 정확성이 낮다. |