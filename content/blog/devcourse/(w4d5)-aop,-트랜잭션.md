---
title: (W4D5) AOP, 트랜잭션
date: 2021-08-31 02:08:02
category: devcourse
draft: false
---

## AOP(Aspect Oreint Programming)

각각의 레이어의 핵심기능과 부가기능을 분리해서 프로그램을 만들 수 있게 해주는 프로그래밍 패러다임이다.

핵심로직만 남기고 부가기능을 별도로 빼서 부가기능을 담당하는 모듈에서 관리하고 발전시킨다.
핵심기능에 부가기능을 적용 -> 어떻게? 

### AOP 적용 방법 (Weaving)

- 컴파일 시점
  - AOP 프레임워크 - AspectJ : 부가기능을 소스에 삽입한다.
- 클래스 로딩 시점
  - 바이트 코드에 부가기능을 삽입한다.
- 런타임 시점
  - 스프링에서 제공하는 AOP : Proxy를 이용
    - JDK dynamic Proxy (interface based)
    - CGLib Proxy (class based)

JdkProxyTest -> 프록시 객체 만들기

AOP - 스키마 기반(XML방식) / AspectJ 어노테이션 으로 구현 가능. @AspectJ 어노테이션을 많이 사용한다.



### AOP 중요 용어

타겟(Target)

- 핵심 기능을 담고 있는 모듈로서 부가기능을 부여할 대상

조인트포인트(Joint Point)

- 어드바이스가 적용될 수 있는 위치
- 타겟 객체가 구현한 인터페이스의 모든 메서드들이 조인트포인트가 된다.

포인트 컷(Pointcut)

- 어드바이스를 적용할 타겟의 메서드를 선별하는 정규표현식(AOP에서 정의한 표현식)
- 포인트컷 표현식은 execution으로 시작하고 메서드의 Signature를 비교하는 방법을 주로 이용한다.
- 여러 조인트포인트 중에 어디에 부가기능을 적용시킬지 나타내준다.

애스펙트(Aspect) - 클래스 정의

- 하나의 자바 클래스. 부가기능 세트를 모듈화 시킨 것
- 여러 메서드들이 부가기능 -> 메서드가 어드바이스
- 애스펙트 = 어드바이스 + 포인트컷
- Spring에서는 Aspect를 빈으로 등록해서 사용한다.

어드바이스(Advice)

- 실제 부가기능
- 어드바이스는 타켓의 특정 조인트포인트에 포인트컷을 이용해서 부가기능을 제공한다.
- @Before, @After, @Around, @AfterReturning, @AfterThrowing 등이 있다.



Weaving

- 타겟의 조인트 포인트에 어드바이스를 적용하는 과정
- AOP를 전체적으로 적용하는 것이라고 보면 된다.



@Around("포인트컷표현식") -> ProceedingJoingPoint를 메서드 인자로

포인트컷표현식의 포인트컷지정자 : 타겟(비즈니스 로직을 담고있는)의 여러 조인트포인트 중에서 어드바이스를 어떻게 적용시킬지 AOP에게 알려주는 키워드

```
지정자(접근지정자 반환형 패키지명 클래스명 메서드명)
```

- `..` : 하위 어떤 패키지든 상관 없을 때
- `*` : '모든'의 의미

**AOP는 등록된 빈이 만든 객체에만 프록시객체가 만들어져서 적용됨**

Spring AOP는 인터페이스 기반이기 때문에 public 밖에 되지 않는다.

execution PCD - 여러가지가 있어서 한 번 읽어볼 것

AspectJ에서는 포인트컷을 따로 정의할 수 있다. -> void로

프로젝트 전체에서 사용될 수 있는 포인트컷들을 모듈화시킬 수도 있다.

Custom Annotation을 정의하여 AOP 적용 -> 왜 Custom Annotation을 정의하여 AOP를 적용하지? (참고 : https://jaehun2841.github.io/2018/07/24/2018-07-24-spring-aop5/#custom-annotation)

- @Target, @Retention



## 트랜잭션

### 트랜잭션 매니저

플랫폼 트랜잭션 매니저(인터페이스)를 통해 트랜잭션을 관리한다.

- @Repository에서 TransactionManager dependency를 가지게 한다. (생성자 주입)
- 트랜잭션 처리를 위해서 TransactionManager에서 getTransaction - 먼저 TransactionDefinition을 만들어야 한다. (new DefaultTransactionDefinition()) -> 트랜잭션 하나를 생성한다고 생각하면 됨.
- 성공하면 커밋 : transactionManager.commit(transaction);
- 에러가 나면 롤백 -> transaction.rollback(transaction);

commit, rollback을 직접 적용하지 않고, try-catch를 하지 않고 템플릿 콜백 패턴 적용 -> TransactionManager 조차 템플릿을 제공해준다. Transaction 템플릿을 사용한다.

- TransactionManager 대신 TransactionTemplate을 dependency로 받는다. 
- transactionTemplate.execute(new TransactionCallbackWithoutResult())
  - TransactionCallbackWithoutResult : return 받는게 없을 때
  - TransactionCallback : return 있을 떄
  - 콜백 메서드 내에 쿼리 명령 코드 삽입
- TransactionTemplate 빈 등록 : 인자로 TransactionManager 전달 (TransactionDefinition도 전달 가능)



### @Transactional - 선언적 트랜잭션 관리

service 레이어에서 많이 사용된다. 

AOP 기술, Proxy 원리와 비슷하다.

잘못되면 전체 롤백 -> 서비스를 호출하는 쪽(caller)에서 보고 확인하게 한다.

@EnableTransactionManagement : Transaction AOP가 적용된다. Transactional 어노테이션을 사용할 수 있게 된다.

@Transactional을 붙이면 Proxy가 만들어지고 Spring AOP에 의해서 자동으로 생성되는 Proxy는 @Transactional이 지시하는 코드가 삽입된다. -> 개발자는 비즈니스 로직에만 집중할 수 있게 된다.



### 트랜잭션 전파 (Transaction Propagation)

특정 메인 메소드 내에서 트랜잭션이 처리되는 과정 안에 또 다른 트랜잭션이 처리되는 경우를 의미한다.

```
@Transactional(propagation = )
```

| 값            | 설명                                                         |
| ------------- | ------------------------------------------------------------ |
| REQUIRED      | @Transactional 기본값. 트랜잭션이 필요하다는 것을 의미한다. 현재 진행 중인 트랜잭션이 있다면 이 트랜잭션을 사용하고, 없는 경우에는 새로운 트랜잭션이 시작된다. |
| MANDATORY     | 호출 전에 반드시 진행 중인 트랜잭션이 존재해야 한다. 진행 중인 트랜잭션이 존재하지 않을 경우 예외가 발생한다. |
| REQUIRED_NEW  | 항상 새로운 트랜잭션이 시작된다. 이미 진행 중인 트랜잭션이 있으면 그 트랜잭션은 해당 메서드가 반환되기 전에 잠시 중단되고 새로운 트랜잭션이 시작된다. 새로 시작된 트랜잭션이 종료되고 나서 기존 트랜잭션이 이어서 동작한다. |
| SUPPORTS      | 트랜잭션이 꼭 필요하지는 않지만, 만약 진행 중인 트랜잭션이 있는 경우에는 해당 트랜잭션을 사용한다. |
| NOT_SUPPORTED | 트랜잭션이 필요로 하지 않다는 것을 의미한다. 진행 중인 트랜잭션이 있다면 해당 메서드가 반환되기 전까지 잠시 중단하고 메서드 실행이 종료가 되고 난 후 기존 트랜잭션을 계속 진행하게 된다. |
| NEVER         | 트랜잭션 진행 상황에서 실행될 수 없다. 만약 이미 진행 중인 트랜잭션이 존재하면 예외가 발생한다. |
| NESTED        | 이미 진행 중인 트랜잭션이 존재하면 중첩된 트랜잭션에서 실행되어야 함을 의미한다. 중첩된 트랜잭션은 본 트랜잭션과 독립적으로 커밋되거나 롤백될 수 있다. 만약 본 트랜잭션이 없는 상황이라면 REQUIRED와 동일하게 작동한다. 그러나 이 전파방식은 DB 벤더 의존적이며, 지원이 안되는 경우도 많다. |

대부분 Required 로 해결이 된다.



### 트랜잭션 격리 (Transaction Isolation Level)

트랜잭션이 시작된 지점의 commit과 rollback 하지 않은 상태에서 다른 트랜잭션에서 동일한 자원에 접근할 때 

다른 트랜잭션에서 변경된 것에 의해 영향을 받지 않게 되는 것 -> 개별 트랜잭션이 독립되었다.

독립성, 격리성에 단계를 나눈 것이 Transaction Isolation Level이다.

4개 격리 수준 / 3개 상황

|                  | dirty read | non-repeatable read | phantom read |
| ---------------- | ---------- | ------------------- | ------------ |
| READ_UNCOMMITTED | yes        | yes                 | yes          |
| READ_COMMITTED   | no         | yes                 | yes          |
| REPEATABLE_READ  | no         | no                  | yes          |
| SERIALIZABLE     | no         | no                  | no           |

- READ_UNCOMMITTED
  - 커밋되지 않은 데이터에 접근이 가능한 수준
  - N개의 트랜잭션이 하나의 공유 데이터에 접근해도 전혀 보호되지 않는다.
- READ_COMMITTED
  - 커밋된 데이터만 조회할 수 있다.
  - dirty read는 발생하지 않는다.
- REPEATABLE_READ
  - 트랜잭션이 시작되고 종료되기 전까지 한 번 조회한 값은 계속 같은 값이 조회된다.
  - 트랜잭션 시작 전에 커밋된 내용에 한해서만 조회된다.
  - 데이터를 변경하려고 하면 UNDO 영역에 백업해두고 실제 레코드를 변경하게 된다.
  - non-repeatable read는 발생하지 않는다.
  - UPDATE한 데이터에 대해서는 정합성을 보장하지만, INSERT/DELETE는 보장되지 않는다.
  - phantom read 문제가 발생한다. (데이터가 사라지거나 없던 데이터가 생기는 현상)
- SERIALIZABLE
  - 트랜잭션이 특정 테이블을 읽으면 다른 트랜잭션은 그 테이블의 데이터를 추가/변경/삭제할 수 없다.
  - 데이터의 정합성을 가장 잘 보장한다.
  - 동시 처리 성능이 가장 떨어진다.

```
@Transactional(isolation = )
```

데이터 정합성과 동시 처리 성능은 반비례하기 때문에 어떤 격리 수준이 좋고 나쁘다를 말하기는 어렵다. 데이터를 어떻게 다룰지에 따라 적절한 전략을 선택하는 것이 중요하다.

> 참고 : https://private-space.tistory.com/97) 