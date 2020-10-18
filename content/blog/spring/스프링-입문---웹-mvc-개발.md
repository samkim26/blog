---
title: 스프링 입문 - 회원 관리 예제(웹 MVC 개발)
date: 2020-10-17 16:10:06
category: spring
draft: false
---

## 회원 웹 기능 - 홈 화면 추가

HomeController 생성   
- 이전에 static - index.html(웰컴페이지)을 만들어 놓은 게 있어서 우선순위에 따라 페이지가 열린다.
- 먼저 요청이 오면 스프링 컨테이너의 관련 컨트롤러를 먼저 찾고, 없으면 static 을 찾는다.
- 그래서 기존 정적 index.html은 무시된다.


## 회원 웹 기능 - 등록

회원 등록 페이지에서 데이터를 전달 받을 폼 객체 생성

@PostMapping, 폼 객체를 전달받아 데이터를 저장(회원 등록)

HTTP - GET/POST 방식


## 출처

> 인프런 '스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술'