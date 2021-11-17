---
title: AnonymousAuthenticationFilter, ExceptionTranslationFilter
date: 2021-11-16 23:11:04
category: spring security
draft: false
---

## AnonymousAuthenticationFilter

익명 사용자에 대한 처리를 하는 필터

```
Detects if there is no `Authentication` object in the `SecurityContextHolder`, and populates it with one if needed.
```

- `SeucotyContext`에 Authentication이 null인 경우 `AnonymousAuthenticationToken`을 주입해주는 역할을 담당

- 클라이언트 요청을 전달 받음
- Authentication 객체가 SecurityContextHolder에 존재하는지 확인
- 존재하지 않는다면 인증을 받지 않은 사용자로 인식, AnonymousAuthenticationToken 생성하여 SecurityContext에 저장

해당 필터에 요청이 도달할때까지 사용자가 인증되지 않았다면, 사용자를 null 대신 Anonymous 인증 타입으로 표현

사용자가 null 인지 확인하는것보다 어떤 구체적인 타입으로 확인할 수 있도록 함



## ExceptionTranslationFilter

인증/인가 예외 처리 필터

FilterChainProxy가 호출하는도중 발생하는 예외를 처리하는 예외 처리 필터

FilterSecurityInterceptor 바로 위에 위치하며, FilterSecurityInterceptor 실행 중 발생할 수 있는 예외를 잡고 처리

>  ExceptionTranslationFilter는 필터 체인 실행 스택에서 자기 아래에 오는 필터들에서 발생하는 예외들에 대해서만 처리할 수 있음. 커스텀 필터를 추가해야 하는 경우 이 내용을 잘 기억하고, 커스텀 필터를 적당한 위치에 두어야 함

- AuthenticationException - 인증 예외
  - 인증 과정중 AuthenticationException 예외가 발생한다면, AuthenticationEntryPoint를 실행하여 인증을 유도한다.
- AccessDeniedException - 인가 예외
  - 인가 과정중 AccessDeniedException 예외가 발생하면 먼저 현재 Authentication이 익명사용자 (AnonymousUser) 인지 확인한다.

- AuthenticationException 예외는 인증 관련 예외이며, 사용자를 로그인 페이지로 보냄
- AccessDeniedException 예외는 AccessDecisionManager에 의해 접근 거부가 발생했을 때 접근 거부 페이지를 보여주거나 사용자를 로그인 페이지로 보냄

- AuthenticationEntryPoint

  - 인증되지 않은 사용자 요청을 처리할때 핵심 적인 역할을 수행함 — 보통 사용자를 로그인 요청 페이지로 포워딩하는 역할을 함

  - 폼 기반 로그인 인증 외의 다른 인증 매커니즘을 처리해야 할때도 AuthenticationEntryPoint를 이용할 수 있음
    - 예를 들어 CAS 인증 처리가 필요하다면 CAS 포탈로 사용자를 이동시킴
    - 서드 파티 시스템과 연동이 필요한 경우 AuthenticationEntryPoint를 직접 구현할 수도 있음