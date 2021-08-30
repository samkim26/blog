---
title: (W4D4) NamedParameterJdbcTemplate
date: 2021-08-31 02:08:21
category: devcourse
draft: false
---

## Embedded DataBase

테스트 시 외부 데이터베이스에 의존하지 않게 만들 수 있다.

H2, wix-embedded-mysql



## NamedParameterJdbcTemplate

기존 JdbcTemplate은 인덱스 기반(?  - placeholder)

**이름 기반의 파라미터를 설정할 수 있게 해주는 JdbcTemplate**

JdbcTemplate을 이용해서 파라미터 부분만 변경해주는 역할을 한다.

바인딩시 ? 를 사용하지 않고 :parameter 를 사용. 세 번째 파라미터로 RowMapper<>와 함께 활용

RowMapper : sql문에 바인딩 할 값이 있을 경우에 바인딩 할 값을 전달할 목적으로 사용하고 있는 객체



## 스프링에서의 예외처리 - DataAccessException

런타임 예외. JdbcTemplate과 콜백 안에서 발생하는 모든 SQLException을 DataAccessException으로 포장해서 던져준다.

Spring에서 DB 관련 Exception은 DataAccessException으로 감싸줘서 알려준다고 보면 된다.



## 트랜잭션 처리

데이터베이스 내에서 하나의 그룹으로 처리되어야 하는 명령문들을 모아놓은 논리적인 작업 단위를 트랜잭션이라고 한다.

---

+) Map 초기화 시 double-brace문은 안티패턴이다. 아래와 같은 방법으로 초기화해야 한다.

- Map.of() - 최대 10개 엔트리
- Map.ofEntries( new AbstractMap.SimpleEntry<String, String>(key, value), ... )
- -> immutable 객체로 만들어진다.

```java
// Map.of() 내에서는 삼항연산자 안먹힘 -> immutable이니까..?
"lastLoginAt", customer.getLastLoginAt() != null ? Timestamp.valueOf(customer.getLastLoginAt()) : null);
```

```java
// 마찬가지로 삼항연산 적용 안됨
new AbstractMap.SimpleEntry<String, Object>("lastLoginAt", customer.getLastLoginAt() != null ? Timestamp.valueOf(customer.getLastLoginAt()) : null)
```

```java
// mutable 객체
var paramMap = new HashMap<String, Object>(
  Map.ofEntries(
    new AbstractMap.SimpleEntry<String, Object>("customerId", customer.getCustomerId().toString().getBytes()),
    new AbstractMap.SimpleEntry<String, Object>("name", customer.getName()),
    new AbstractMap.SimpleEntry<String, Object>("email", customer.getEmail())
  )
);

// put으로 삼항연산자 이후 put
paramMap.put("lastLoginAt", customer.getLastLoginAt() != null ? Timestamp.valueOf(customer.getLastLoginAt()) : null);
```

- new HashMap<String, Ojbect> (Map.ofEntries()) : HashMap으로 감싸면 mutable 객체로 만들어진다. -> put 가능

> 참고 https://www.baeldung.com/java-initialize-hashmap