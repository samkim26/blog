---
title: (W5D1) 웹, Servlet
date: 2021-08-31 15:08:67
category: devcourse
draft: false
---

## 웹 기술

HTTP는 URI로 조작 대상을 지정한다. 특정한 리소스를 가져와서 변경, 삭제할 수 있다.

### HTTP 주요 특징

- TCP/IP 기반
- 요청/응답형 프로토콜
- 동기형 프로토콜
- stateless

### HTTP Method

주요 - GET POST PUT DELETE

| CRUD명 | 의미 | 메서드   |
| ------ | ---- | -------- |
| Create | 작성 | POST/PUT |
| Read   | 읽기 | GET      |
| Update | 갱신 | PUT      |
| Delete | 삭제 | DELETE   |

### 웹의 기술적 특징

- Hypermedia System
- Distributed System - 복수의 컴퓨터로 연산



웹 서버 vs 웹 애플리케이션 서버 -> 동적 컨텐츠를 제공하냐 안하냐의 차이

개발한 프로젝트 빌드 -> was에 deploy

was - 별도 서버로 구동되어 있음

war로 빌드해서 deploy, 웹 컨테이너가 로드되어 코드가 동작한다.

was가 웹 서버의 역할도 한다. - 웹 서버를 포함한다는 것은 아니다.

정적인 데이터는 웹 서버에서 처리하고 WAS로 서비스 요청이 넘어가지 않게 한다.

동적인 데이터는 WAS가 처리한다. 웹 채플리케이션의 수행에 집중할 수 있다.


## Servlet

클라이언트의 요청을 받아서 특정 서비스 기능을 호출을 해주는 컴포넌트

http 요청에 대해 응답 처리를 하는 java software 컴포넌트

was 안에서 servlet이 구동된다.

servlet - java interface

servlet 구현체로 HttpServlet

web container

MVC Pattern -> 역할 분리

- Model : Java 객체 또는 Pojo로 데이터를 담는다.
- View : Model이 담고 있는 데이터를 시각적으로 보여준다.
- Controller : Model과 View를 연결하여 클라이언트의 요청에 따른 응답을 처리한다.

### Servlet Lifecycle

- init()
- service()
- doGet() / doPost()

Servlet 실습

- Servlet을 사용하기 위해서 프로젝트의 pom.xml에 라이브러리 추가
- 서블릿 컨테이너에 서블릿이 존재한다는 것을 알려줘야 함 -> web.xml
- web.xml 을 was에 알려줘야 함(deploy) - 서버를 구동하고 deploy
- web.xml에 등록하지 않고 @WebServlet 어노테이션을 이용하는 방법
- Spring에서 제공해주는 인터페이스를 구현하는 방법도 있다. (WebApplicationInitializer)

-> 서버에 tomcat설치하고 tomcat에 war를 deploy하는 방식

Servlet을 직접적으로 사용하지 않고 Spring이 이것을 추상화시킨 SpringMVC라는 것을 제공해준다.

SpringBoot에서는 Embedded Tomcat을 사용할 것이다. WAS에 배포할 일은 없다.(레거시 애플리케이션에서는 WAS 배포하기도 함) 

모던 아키텍처 - 마이크로 서비스 아키텍처 환경에서는 Embedded Tomcat, JAR로 배포한다.