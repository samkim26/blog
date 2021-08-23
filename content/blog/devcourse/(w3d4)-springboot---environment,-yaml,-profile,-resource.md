---
title: (W3D4) SpringBoot - Environment, YAML, Profile, Resource
date: 2021-08-19 22:08:05
category: devcourse
draft: false
---

## Environment

ApplicationContext에서 제공하는 또 다른 중요한 기능 중의 하나

Environment는 활성화할 프로파일을 확인하고 설정해주는 것이다.

EnvironmentCapable의 기능

1. 프로파일(profile)
2. 프로퍼티(property)

스프링 환경은 profile과 property로 제공된다

profile에 따라서 환경이 바뀌고, 그것에 따라 property가 바뀐다. 

환경에 따라서 profile과 property 소스가 다르게 설정된 environment가 사용된다.

애플리케이션에서 속성 정의하는 파일 - properties 파일 , yaml 파일

프로퍼티 파일은 key-value 형식으로 작성

DB 접속 정보, 포트 정보 등을 property로 정리하고 설정한다. 

프로퍼티를 읽으라고 스프링에게 알려줘야한다. - @PropertySource 어노테이션 사용

@Value 어노테이션 사용 -> 프로퍼티 값을 필드에 주입시킬 수 있다. 생성자를 만들지 않아도 값이 주입된다.

@Value 어노테이션은 파라미터에 쓸 수도 있다.



## YAML로 프로퍼티 작성

YAML 파일은 @PropertySorce 어노테이션으로 불러올 수 없다.

yaml과 같이 지원하지 않는 포맷은 스프링에서 PropertySourceFactory로 구현해야한다.

리스트는 형변환이 바로 되지 않는다. -> 스프링부트에서 제공해주는 형태로

클래스에 @ConfigurationProperties 어노테이션을 붙여서 YAML파일과 바인딩할 수 있게 한다.

@EnableConfigurationProperties 어노테이션을 사용해서 @ConfigurationProperties을 처리할 수 있게 만들어준다.

프로퍼티를 그룹화시킬 때 클래스로 만들고 주입받아서 쓴다. - 큰 프로젝트에서 쓰인다.

작은 프로젝트는  @Value 어노테이션으로 충분



## Profile

스프링 프로파일은 애플리케이션 설정 일부를 분리하여 특정 환경에서만 사용 가능하게 한다. 

프로파일을 이용하면 여러 빈 정의들이 특정 프로파일에서만 동작하게 할수도 있고 특정 프로퍼티들을 특정 프로파일로 정의해서 해당 프로파일이 엑티브일 때 적용되게 할 수도 있다.

스프링 프레임워크에서는 profile 대한 빈 정의만 지원하고 있고,  profile별로 property의 변경은 스프링부트에서 지원한다.

스프링부트에서 실행해야 다른 프로파일로 인한 설정들을 읽을 수 있다.



## Resource

애플리케이션을 만들다 보면 외부 리소스를 읽을 필요가 있다. 다양한 형태의 파일을 가져올 때 호출해야 할 API가 각각 다른데, 스프링은 이런 어려움을 `Resource` 와 `ResourceLoader` 인터페이스를 통해 하나의 API로 제공한다.

ResourceLoader : 리소스를 가져온다.

Resource의 다양한 구현체를 제공한다.

모든 ApplicationContext에서 Resource 객체를 가져올 수 있다.

working directory 기준으로 파일을 찾는다.

리소스를 ApplicationContext를 통해 쉽게 가져올 수 있다.