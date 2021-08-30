---
title: (W4D3) DataSource, TestInstance LifeCycle, JDBC Template
date: 2021-08-30 15:08:64
category: devcourse
draft: false
---

## DataSource

connection을 매번 생성하고 close 하는 과정은 많은 리소스가 소비된다. -> connection pool을 통해서 관리한다.

DBCP(DataBase Connection Pool) : DB와 커넥션을 맺고 있는 객체를 관리하는 역할

DataSource : connection을 관리한다. DataSource를 통해서 connection을 얻거나 반납할 수 있다.

DBCP에는 다양한 구현체들이 존재한다. (라이브러리가 많다.)

### HikariCP

빠르고 안정적인 DBCP



## TestInstance Lifecycle

TestInstance는 테스트 인스턴스의 Lifecycle을 설정할 때 사용한다.

- PER_METHOD : test 메서드 당 인스턴스가 생성된다.
- PER_CLASS : test 클래스 당 인스턴스가 생성된다.

```java
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
```

Lifecycle을 클래스 단위로 지정해놓으면 @BeforeAll, @AfterAll을 static method가 아닌 곳에서도 사용할 수 있다.

@TestMethodOrder(MethodOrderer.OrderAnnotation.class) : 테스트 순서 보장 -> 테스트 메소드에 @Order(n) 에 따라 실행



## JDBC Template

변경이 필요한 코드만 제공해서 간결한 코드를 작성하게 해준다.

반복되는 부분, 변경되는 부분 -> **템플릿 콜백 패턴**을 이용해서 JDBC Template를 만들었다.

jdbcTemplate.queryForObject() : 1개의 결과를 가져온다.  

```
<템플릿 콜백 패턴>
전략 패턴의 변형. DI에서 사용하는 특별한 전략 패턴이다. 
전략 패턴에 익명 내부 클래스를 가미해서 사용한다.
OCP, DIP가 적용된 설계 패턴이다.
```

