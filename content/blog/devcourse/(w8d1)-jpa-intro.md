---
title: (W8D1) JPA Intro
date: 2021-09-28 15:09:55
category: devcourse
draft: false
---

## JPA의 필요성

### JDBC

- connection 획득
- Statement를 이용한 질의
- ResultSet을 이용한 질의 결과 사용
- Statement, Connection 반납

등과 같은 과정 - 반복 작업이 존재한다.

### JDBC Template

기존 JDBC를 이용했을때의 반복적인 작업을 JDBC Template이 대신 수행한다.

자바 코드상에 SQL문이 작성되어 유지보수가 어렵다.

### MyBatis (QueryMapper)

JDBC의 반복적인 작업을 Mybatis가 대신 수행한다. 자바 코드와 쿼리를 분리하게 된다. 쿼리 수정으로 자바 코드 수정 이나 컴파일 하는 작업을 하지  않아도 된다.

- Annotation 사용 - 어노테이션 파라미터로 쿼리 작성
- XML 사용 - XML 파일에 쿼리 작성, 어떤 인터페이스와 매핑되는지 설정

메소드 호출을 통해서 쿼리 실행

RDB와 자바 객체가 가지는 패러다임의 불일치가 존재한다. -> JPA를 통해 극복


## JPA

- 생산성 증진
  - SQL에 의존적인 개발에서 탈피하여, 객체중심으로 생산적인 개발이 가능하다.
- 객체와 관계형테이블의 패러다임 불일치
  - 객체지향 프로그래밍은 추상화, 캡슐화, 상속, 다형성 등을 제공한다.
  - 관계형 데이터베이스 데이터 중심으로 구조화 되어있으며, OOP의 특징을 지원하지 않는다.

