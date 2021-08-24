---
title: (W4D2) JDBC
date: 2021-08-24 17:08:99
category: devcourse
draft: false
---

## JDBC

자바 애플리케이션이 데이터베이스와 연결할 수 있도록 한다. 

표준 인터페이스 - DBMS의 종류에 상관없이 SQL문을 실행시키고 처리할 수 있다.

JDBC DB Driver : DBMS 벤더사에서 개발하고 배포함

JDBC API를 이용해서 Driver와 커넥션을 맺고 쿼리에 대한 요청을 하게 한다.

### JDBC Flow

- DriverManager를 통해서 커넥션 객체를 받아온다.
- Connection을 통해서 Statement를 가져온다.
- Statement를 통해서 쿼리를 실행시켜서 ResultSet을 가져오거나 Update를 실행한다.
- 데이터베이스 커넥션을 종료한다.



## Statement vs PrepareStatement

- Statement : 매번 쿼리가 수행할 때마다 쿼리의 문장을 분석하고 컴파일, 실행 이렇게 세 단계의 실행 단계를 거치게 된다.
- **PrepareStatement** : 처음 한 번만 세 단계를 거치고 캐시에 담겨서 재사용된다. 처음에 만들어진 쿼리가 고정된다. 쿼리를 중간에 바꿀 수 없고(다이나믹 쿼리 불가능) 매번 실행 단계를 거치지 않아 성능이 좋다.

=> **캐시 사용 여부**가 가장 큰 차이점이다.



## Try with resources

try-catch-finally

- connection 객체를 생성하고 finally 블록에서 close() 해야한다.
- null 체크, close에 대한 Exception 처리까지 해야했다.

try-with-resources

- try( )에서 선언된 객체들에 대해서 try가 종료될 때 자동으로 자원을 해제한다. try 구문이 종료될 때 객체의 close() 메소드를 호출해준다. -> finally에 명시적으로 close()를 할 필요가 없다.
- **AutoCloseable** 인터페이스를 구현한 객체만 close()가 호출된다.