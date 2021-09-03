---
title: (W5D5) SpringBootApplication
date: 2021-09-03 17:09:12
category: devcourse
draft: false
---

## @SpringBootApplication

@SpringBootApplication 하나로 Spring Boot Application을 실행할 수 있다.

Spring Boot의 자동 설정, Spring Bean 읽기와 생성이 모두 자동으로 설정된다.

- @SpringBootConfiguration
- @ComponentScan
- @EnableAutoConfiguration

### @SpingBootConfiguration

Spring Boot의 설정을 나타낸다.

### @EnableAutoconfiguration

사전에 정의한 라이브러리들을 Bean으로 등록해 준다. 사전에 정의한 라이브러리들 모두가 등록되는 것은 아니고 특정 Condition이 만족될 경우에 빈으로 등록한다.

- @OnBeanCondition : 특정 Bean이 사전에 생성되어있지 않을 경우에 조건이 만족된다.
- @ConditionalOnBean : 특정 Bean이 이미 생성되어있을 경우에 조건이 만족된다.
- @ConditionalOnClass : Classpath에 특정 class가 존재할 경우에 조건이 만족된다.

`exclude = `를 사용해서 비활성화 하고 싶은 클래스를 고를 수 있다.
@EnableAutoConfiguration(exclude={ })

### @ComponentScan

루트 패키지 이하의 클래스 중 @Component 어노테이션이 붙은 클래스들을 찾아서 Bean으로 등록한다.

@Component, @Configuration, @Repository, @Service, @Controller, @RestController

@SpringBootApplication의 핵심은 AutoConfiguration에 있다.
pom.xml의 starter를 통해서 dependency와 버전에 대한 관리를 해주고 AutoConfiguration에 의해서 자동으로 빈을 설정하고 프로퍼티들의 타입화 등을 특정 컨디션에 맞게 알아서 설정되게 하는 것이다.

