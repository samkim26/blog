---
title: (W14D5) Spring Security Architecture
date: 2021-11-15 03:11:39
category: devcourse
draft: false
---

## Spring Security Architecture

### Conceptual Architecture

- AuthenticationManager : 사용자 인증 관련 처리
- AccessDecisionManager : 사용자가 보호받는 리소스에 접근할 수 있는 적절한 권한이 있는지 확인

### FilterChainProxy (Spring Security FilterChain)

- Spring Security의 실제적인 구현은 서블릿 필터를 통해 이루어짐 - Filter 구현체들의 집합
- FilterChainProxy 세부 내용은 WebSecurityConfigurerAdapter 추상 클래스를 상속하는 구현체에서 설정함
  - 웹 요청은 필터 체인을 차례로 통과하게 됨
  - 모든 필터를 통과하게 되지만 모든 필터가 동작하는 것은 아님
  - 각 필터는 웹 요청에 따라 동작 여부를 결정할 수 있고, 동작할 필요가 없다면 다음 필터로 넘김
- 요청을 처리하고 응답을 반환하면 필터 체인 호출 스택은 모든 필터에 대해 역순으로 진행
- Bean 등록 - `springSecurityFilterChain` 
- 서블릿 컨테이너가 웹 요청 수신 -> DelegatingFilterProxy(Filter 구현체, Spring Security 한정적 필터가 아님)로 전달 -> DelegatingFilterProxy가 스프링 시큐리티 필터 체인을 가리키고 있어서 스프링 시큐리티 필터 체인 목록으로 웹 요청을 전달

### FilterChainProxy를 구성하는 Filter 목록

| 필터 이름                               | 설명                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| ChannelProcessingFilter                 | 웹 요청이 어떤 프로토콜로 (http 또는 https) 전달되어야 하는지 처리 |
| SecurityContextPersistenceFilter        | SecurityContextRepository를 통해 SecurityContext를 Load/Save 처리 |
| LogoutFilter                            | 로그아웃 URL로 요청을 감시하여 매칭되는 요청이 있으면 해당 사용자를 로그아웃 시킴 |
| UsernamePasswordAuthenticationFilter    | ID/비밀번호 기반 Form 인증 요청 URL(기본값: /login) 을 감시하여 사용자를 인증함 |
| DefaultLoginPageGeneratingFilter        | 로그인을 수행하는데 필요한 HTML을 생성함                     |
| RequestCacheAwareFilter                 | 로그인 성공 이후 인증 요청에 의해 가로채어진 사용자의 원래 요청으로 이동하기 위해 사용됨 |
| SecurityContextHolderAwareRequestFilter | 서블릿 3 API 지원을 위해 HttpServletRequest를 HttpServletRequestWrapper 하위 클래스로 감쌈 |
| RememberMeAuthenticationFilter          | 요청의 일부로 remeber-me 쿠키 제공 여부를 확인하고, 쿠키가 있으면 사용자 인증을 시도함 |
| AnonymousAuthenticationFilter           | 해당  인증 필터에 도달할때까지 사용자가 아직 인증되지 않았다면, 익명 사용자로 처리하도록 함 |
| ExceptionTranslationFilter              | 요청을 처리하는 도중 발생할 수 있는 예외에 대한 라우팅과 위임을 처리함 |
| FilterSecurityInterceptor               | 접근 권한 확인을 위해 요청을 AccessDecisionManager로 위임    |

- RequestCacheAwareFilter

  - 익명 사용자가 보호 받는 리소스에 접근할 경우 FilterSecurityInterceptor에서 접근 거부 예외 발생 -> 예외는 그 위의 ExceptionTranslationFilter에서 처리함

  - 원래 접근하려고 했던 페이지(요청)를 캐시 처리하고, 로그인을 정상적으로 하게 되면 저장된 요청을 읽어서 캐시된 요청을 처리 / 캐시된 요청이 없다면 현재 요청을 처리함

- ChannelProcessingFilter
  - 전송 레이어 보안 적용을 위한 Filter
  - 웹 요청이 어떤 프로토콜(http 또는 https)로 처리할지 분기함
    - HTTP : 클라이언트와 서버가 주고 받는 데이터는 암호화 되어있지 않음
    - HTTPS : HTTP 프로토콜의 암호화 버전. 데이터 암호화를 위해 SSL을 사용
    - 서버에 SSL 인증서 설치

