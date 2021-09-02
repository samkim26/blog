---
title: (W5D3) WebApplicationContext, REST API
date: 2021-09-01 02:09:70
category: devcourse
draft: false
---

## WebApplicationContext

ApplicationContext를 상속

ServletContext에 접근할 수 있는 기능이 추가된 ApplicationContext

여러 서블릿이 공유가 가능한 정보를 ServletContext에 담는다. (공용 자원) - 여러 DispatcherServlet에서도 접근 가능

모든 WebApplicationContext가 접근 가능한 루트 ApplicationContext가 필요하다. -> ServletContext가 만들어질 때 루트 ApplicatoinContext가 만들어지고 setAttribute()를 통해 SetvletContext에 담겨진다. 

SevletContextListener 인터페이스 : ServletContext가 만들어 질 때 (변경될 때) 호출된다.

ContextLoaderListener(구현클래스)을 통해 웹 애플리케이션 전체에서 사용 가능한 WebApplicationContext를 만든다. 이것을 루트 ApplicationContext라고 부른다.

루트 ApplicationContext를 만들고(service, data access 관련 repository 관리), DispatcherSevlet에 들어가는 ApplicationContext에는 MVC 관련 내용만 등록해서 부모-자식으로 설정하여 관리하게 한다.

setLoadOnStartUp(-1) -> -1이면 처음에 로드가 되지 않고 처음 API 요청이 있을 때 로드가 된다. / 0이나 1이면 처음에 같이 로드된다.



## REST API

REST를 모두 충족하면 RESTful

REST(Representational State Transfer) : 분산 하이퍼미디어 시스템을 위한 소프트웨어 아키텍처의 한 형식

네트워크 아키텍처 원리의 모음

네트워크 아키텍처 원리란 자원을 정의하고 자원에 대한 주소를 지정하는 방법

웹 상의 자료를 HTTP위에서 SOAP나 쿠키를 통한 세션 트래킹 같은 별도의 전송 계층 없이 전송하기 위한 간단한 인터페이스를 의미한다.

REST API = REST 아키텍처 스타일을 따르는 API

### REST 아키텍처 스타일

- 클라이언트-서버
  - 사용자 인터페이스에 대한 관심을 데이터 저장에 대한 관심으로부터 분리함으로써 클라이언트의 이식성과 서버의 규모확장성을 개선한다.
- stateless
  - 클라이언트 서버의 통신에 상태가 없다. 모든 요청에는 필요한 모든 정보를 담고있어 가시성이 좋고, 요청 실패 시 복원이 쉽기 때문에 신뢰성이 좋다. 상태를 저장할 필요가 없어 규모확장성이 개선된다.
- 캐시
  - 캐시가 가능해야 한다. HTTP가 가진 캐싱 기능이 적용 가능하다. HTTP 프로토콜 표준에서 사용하는 Last-Modified태그나 E-Tag를 이용하면 캐싱 구현이 가능하다.
- 균일한 인터페이스(uniform interface)
  - URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일을 의미한다.
- 계층화된 시스템(layered system)
  - REST 서버는 다중 계층으로 구성될 수 있으며 보안, 로드밸런싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있고 PROXY, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 한다.

### Richardson Maturity Model

- Level 0 : The Swamp of POX
- Level 1 : Resources
- Level 2 : HTTP Verbs
- Level 3 : Hypermedia Controls - HATEOAS(Hypermedia as the Engine of Application State) 거의 쓰지 않음

### API 설계

- URI는 정보의 자원을 표현해야 한다. (리소스명은 명사를 사용)
- 자원에 대한 행위는 HTTP Method로 표현한다.
- 슬래시 구분자(/)는 계층 관계를 나타내는 데 사용한다.
- URI 마지막 문자로 슬래시(/)를 포함하지 않는다.
- 하이픈(-)은 URI 가독성을 높이는 데 사용한다.

### RestController

- @RequestBody : HTTP 요청 본문에 담긴 값들을 자바 객체로 변환
- @ResponseBody : 자바 객체를 HTTP 응답 본문의 객체로 변환

HttpMessageConverter - 직렬화와 역직렬화 담당

