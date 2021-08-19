---
title: (W3D4) SpringBoot - Environment, YAML
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

```java
@Configuration
@ConfigurationProperties(prefix = "kdt") // 속성 바인딩
public class OrderProperties implements InitializingBean {

    private String version;

    private int minimumOrderAmount;

    private List<String> supportVendors;

    private String description;
```

프로퍼티를 그룹화시킬 때 클래스로 만들고 주입받아서 쓴다. - 큰 프로젝트에서 쓰인다.

작은 프로젝트는  @Value 어노테이션으로 충분

