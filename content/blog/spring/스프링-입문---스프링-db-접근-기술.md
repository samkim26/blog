---
title: 스프링 입문 - 스프링 DB 접근 기술
date: 2020-10-20 13:10:75
category: spring
draft: false
---

## H2 데이터베이스 설치

교육용, 용량작고 가볍다. 개발이나 테스트 용도로 가볍고 편리한 DB, 웹 화면 제공
- https://www.h2database.com
- 다운로드 및 설치
- h2 데이터베이스 버전은 스프링 부트 버전에 맞춘다.
- 실행: h2.bat (Windows)

### 데이터베이스 파일 생성 방법
- jdbc:h2:~/test (최초 한번)
- ~/test.mv.db 파일 생성 확인
- 이후부터는 jdbc:h2:tcp://localhost/~/test 이렇게 접속 (파일 직접 접근이 아니라 웹 소켓으로 접속)
- 문제가 있으면 파일 삭제($rm ~/test.mv.db) 후 서버 껐다켜서 처음부터 다시 세팅

테이블 생성
- JAVA에서는 Long인데 DB에서는 bigint


## 순수 JDBC

JAVA는 DB랑 붙으려면 JDBC Driver가 꼭 필요, 데이터베이스가 제공하는 클라이언트가 필요

접속 정보 스프링 부트가 다 해줌. 경로만 넣어주면 됨.

- JdbcMemberRepository 클래스 생성
- Datasource를 스프링 부트로 주입 받기
- 리소스는 사용 후 릴리즈 해줘야 함

> 스프링 프레임워크를 통해서 데이터 connection을 쓸 때는 DataSourceUtils를 통해서 connection을 획득해야한다.

### 스프링 설정 변경
SpringConfig 에서 MemoryMemberRepository -> JdbcMemberRepository 로 교체한다.

과거에는 MemberService에서 코드의 수정이 이루어졌다. 이제는 기존의 코드는 손대지 않고 어플리케이션 조립 코드만 조작한다.


## 출처

> 인프런 '스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술'