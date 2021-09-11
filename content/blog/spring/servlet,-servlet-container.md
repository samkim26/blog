---
title: Servlet, Servlet Container
date: 2021-09-11 11:09:68
category: spring
draft: false
---

## Servlet

- HTTP 웹 요청에 응답하는 자바 클래스(인터페이스)
- 서블릿은 일반적으로 메소드 구현 시 일부 처리를 수행한 다음 클라이언트에 응답을 반환한다.

## Selvet Container = 웹 컨테이너 = WAS (tomcat, Jetty 등)

- request, response 객체를 생성하고 관리한다.
- 서블릿 생명주기를 관리한다.
- 특정 서블릿으로 URL 매핑
- 서블릿 인스턴스를 생성한다.
- 서블릿 컨테이너가 시작되는 순간 init() 메서드를 통해 서블릿을 초기화하고 메모리에 로드한다.(클래스 로더)
  - 컨테이너가 시작되는 순간(WAS가 시작되는 순간)인지 클라이언트가 처음 요청할 때인지?
  - -> load-on-startup 으로 서블릿 컨테이너가 실행되면서 미리 서블릿을 실행
- 요청이 올 때 스레드 풀로부터 스레드를 할당받아 서블릿을 실행한다.
- 멀티스레드 환경에서 동작한다.

### web.xml / WebApplicationInitializer

웹 애플리케이션 설정 정보 - WAS(서블릿 컨테이너)가 처음 구동될 때 웹 애플리케이션 설정을 구성한다.

서블릿 등록/URL매핑, 리스터 등록, 필터 등록 작업

- Servlet 정보
- Servlet이 매핑되는 URL 정보
- Filter
- Listener - ContextLoaderListener

**ContextLoaderListener** : 루트 WebApplicationContext(IoC container)에 정의되어 있는 것들(공통 빈)을 모든 서블릿과 필터가 공유할 수 있게 한다.



각각의 서블릿 객체로 요청을 처리하고 응답하는 과정은 여러 문제가 있어 보인다.

- 서블릿 객체를 일일이 생성하고 서블릿 컨테이너(web.xml)에 등록해야 한다.
- 서블릿 객체들이 공통적으로 수행해야하는 작업들이 있다.
- 뷰 코드와 로직 처리를 위한 코드들이 섞여있다. -> 유지보수가 어렵다.
- 객체지향 설계와는 거리가 먼 것 같다.

=> 이러한 문제들을 해결하려는 노력으로 MVC 패턴, Front Controller 패턴, Spring MVC 등 여러 개념들이 나오지 않았을까?