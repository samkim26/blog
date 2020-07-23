---
title: class, object, instance / 잘 설계된 클래스 / Overloading
date: 2020-07-23 10:07:49
category: java
draft: false
---

## class, object, instance 상호관계

현실 세계에 있는 객체를 설계하는 도구가 class

설계한대로 메모리에 객체를 만들기 위해서 변수가 필요하다.   
객체를 담는 변수 - 객체변수에서 '변수'는 생략해서 객체(object)라고 부른다.

객체가 생성되어 메모리에 실체가 만들어진 것을 instance

객체가 instance를 가르키고 있으면 인스턴스(변수)라고 한다.

```java
// 객체생성과정, 인스턴스를 만드는 과정
BookDTO b = new BookDTO();
```

## 잘 설계된 클래스

정보은닉 : 다른 객체로부터 접근을 막는 것

Q. 왜 접근을 막아야 해?  
A. 기억공간의 객체 상태(정보)를 보호하기 위해

멤버변수의 접근제어자를 private 으로 설정한다.

데이터를 저장하기 위해 **setter method**를 활용한다.
**getter method**로 데이터의 값을 얻어온다.


## 오버로딩(Overloading)

Method Overloading
같은 이름의 메소드를 여러 개 가지면서 매개변수의 유형과 개수가 다르도록 하는 기술   
-> 메소드의 **signature** 가 다르면 된다. (**signature** : 매개변수의 타입, 개수)

> **Point** 오버로딩(Overloading) : 정적 바인딩(컴파일 시점에서 호출될 메소드가 이미 결정되어 있는 바인딩) -> 속도와는 관계가 없다


## 동일한 구조, 이질적인 구조

### 배열 VS 클래스의 관계

array : 동일한 데이터 구조
class : 서로 다른(이질적인) 데이터 구조

object 배열 : 객체를 저장하는 배열

---

### 출처
[인프런 - Java TPC](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94-%EC%9E%85%EB%AC%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/)