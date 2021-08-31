---
title: (W5D2) Spring MVC
date: 2021-08-31 15:08:78
category: devcourse
draft: false
---

## Spring MVC

### DispatcherServlet

Front Controller 패턴 - 모든 요청을 받아서 다른 컨트롤러들에게 로직을 처리하는 부분을 위임한다. 발생되는 예외 처리와 어떤 뷰를 선택해서 클라이언트에게 전달할지에 대한 처리를 담당한다. -> 애플리케이션에 들어오는 모든 요청 핸들링, 공통 작업 처리

Spring은 Front Controller 패턴을 사용해서 DispatcherServlet을 제공한다.

개발자는 Servlet을 작성하는 것이 아니라 controller를 작성하고 Spring이 작성한 controller를 호출한다.

사용자의 요청을 일일이 전달하기 위해 web.xml에 많은 servlet을 등록하고 매핑하는 과정이 필요했다. DispatcherServlet은 이러한 과정을 줄일 수 있게 한다.

### Spring MVC 처리 흐름

1. DispatcherServlet의 HTTP 요청 접수
2. DispatcherServlet에서 Controller로 HTTP 요청 위임
3. Controller의 메서드들이 호출되어 모델 생성과 정보 등록
4. Controller의 결과 리턴 : Model, View
5. DispatcherServlet의 View 호출과 Model 참조
6. HTTP 응답 돌려주기

DispatcherServlet을 만드는 두 가지 방법

- web.xml
- **WebApplicationInitializer** - DispatcherServlet를 만들고 하위에 여러 Controller를 등록하는 방식으로 애플리케이션을 만든다.

Spring에서는 Controller를 `핸들러`라고 부른다.

사용자 요청을 기준으로 어떤 핸들러에게 작업을 위임할지를 결정해주는 것 - URL, HTTP method, parameter 등을 참고하여 걸정 => **핸들러 매핑 전략** - `RequestMappinHandlerMapping`

핸들러가 호출될 때 핸들러가 가지고 있는 메서드 파라미터들을 메서드 파라미터에 맞는 정보로 변경해주는 것이 핸들러 어댑터 

HTTP servlet request 타입의 Object를 핸들 어댑터에 전달해주면 어댑터가 컨트롤러의 메서드가 받을 수 있는 파라미터로 변환해서 전달해준다. - `RequestMappingHandlerAdapter`

가장 많이 쓰이는 것이 Annotation 기반

DispatcherServlet에 내부적으로 ViewResolver가 등록되어있다.

DispatcherServlet은 ViewResolver를 통해서 원하는 View를 찾아서 Reponse body를 통해 클라이언트에게 전달

content 타입에 따라서 ViewResolve -> ContentNegotiatingViewResolver

?AnnotationConfigWebApplicationContext 에 대하여

static resource 처리 : ResourceResolver



## Thymeleaf

서버사이드 자바 템플릿 엔진

> 동적 컨텐츠를 생성하는 방법

html 파일을 작성 -> 파싱해서 만들어준다.

JSP와는 다르게 컴파일하지 않고 파싱해서 결과를 전달한다. 표현식이 강력하다.

JSP와 달리 Servlet Code로 변환되지 않다는 점이다. 따라서 비즈니스 로직과 분리되어 오로지 View에 집중할 수 있다.


---

## 추가 자료

### Java Web and Spring overview

- Apache Web Server는 정적 컨텐츠를 효율적으로 제공한다.
- 톰캣은 동적 컨텐츠를 제공한다. 정적 컨텐츠도 다루지만, 덜 효율적이다.
- 서블릿 컨테이너의 세 가지 컴포넌트 : 필터, 서블릿, 리스너
  - 서블릿 컨테이너인 톰캣에 요청이 들어올 때, 먼저 서블릿 필터에서 처리된다.
  - 서블릿은 그 요청을 다루고 응답을 만들어낸다.
- 스프링 웹 애플리케이션에는 두 가지 컨테이너 종류가 존재한다. 각각은 다르게 구성되고 초기화된다. - IOC 컨테이너, MVC 컨테이너
- IOC 컨테이너 : 빈을 인스턴스화하고 구성, 조립하는 책임을 가진다. 애플리케이션마다 하나의 `ApplicationContext`가 존재하게 된다.
- MVC 컨테이너 : 각 `Dispatcherservlet`은 이미 모든 빈이 정의되어 있는 루트 WebApplicationContext를 상속하는 자신만의 `WebApplicationContext`를 가진다.
- `DispatcherServlet`은 모든 요청을 처리하고, 그 요청들을 적절한 채널로 전달한다.



### 스프링 웹 애플리케이션 동작 원리

1. 웹 애플리케이션이 실행되면 Tomcat(WAS)에 의해 web.xml이 로딩된다.(load-on-startup으로 톰캣 시작 시 servlet 생성 가능하도록 설정 가능)
2. web.xml에 등록되어 있는 ContextLoaderListener가 생성된다. ContextLoaderListener 클래스는 ServletContextListener 인터페이스를 구현하고 있으며, ApplicationContext를 생성하는 역할을 수행한다.
3. 생성된 ContextLoaderListener는 applicationContext.xml을 로딩한다.
4. applicationContext.xml에 등록되어 있는 설정에 따라 Spring Container가 구동된다. 이 때, 개발자가 작성한 비즈니스 로징에 대한 부분과 DAO, VO 객체들이 생성된다.
5. 클라이언트로부터 웹애플리케이션 요청이 온다.
6. DispatcherServlet(Servlet)이 생성된다. DispatcherServlet은 FrontController의 역할을 수행한다. 클라이언트로부터 온 요청 메시지를 분석하여 알맞은 PageController에게 전달하고 응답을 받아 요청에 따른 응답을 어떻게 할지 결정만 한다. 실질적인 작업은 PageController에서 이뤄지기 때문이다. 이러한 클래스들을 HandlerMapping, ViewResolver클래스라고 한다.
7. DispatcherServlet은 servlet-context.xml(spring-mvc.xml)을 로딩한다.
8. 두 번째 Spring Container가 구동되면 응답에 맞는 PageController들이 동작한다. 이 때 첫 번째 Spring Container가 구동되면서 생성된 DAO, VO, ServiceImpl 클래스들과 협업하여 알맞은 작업을 처리하게 된다.