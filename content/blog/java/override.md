---
title: Override / Object Casting
date: 2020-08-03 14:08:33
category: java
draft: false
---

**Override(재정의)** : 상속 관계에서 상속받은 하위 클래스가 상위 클래스의 동작을 수정하는 것

부모 메소드 무시 - 메모리에는 부모와 자식 메소드가 **공존**하지만 결국에는 자식 메소드가 실행된다.


```java
// 재정의된 메소드를 찾아감(자식의 메소드를 실행) - upcasting
Animal d = new Dog();
d.eat();
```

동적 바인딩(호출될 메소드가 **실행 시점**에서 결정되는 바인딩)   
프로그램의 속도가 떨어지는 원인이 되지만 이점이 더 많기 때문에 사용한다.

> (참고, 복습) Overloading : 컴파일 시점에 사용될 메소드가 결정되기 때문에 실행속도에는 문제가 없음

자동 형변환 : 상속관계에서 하위 클래스가 상위 클래스 번지로 자동으로 들어가는 것

.class 파일만 제공했을 때 -> 하위 클래스의 동작 방식을 알지 못하더라도 상위 클래스로 하위 클래스를 구동시킬 수 있다.

upcasting(자동형변환) <-> downcasting(강제형변환)

---

**Object Casting (객체 형 변환)** : 상속 관계에 있는 클래스들 간의 형(DataType)을 바꾸는 것

```java
// Upcasting(자동형변환)
Animal r = new Dog();
r = new Cat();

// Dowcasting(강제형변환)
Dog g = (Dog) r;
Cat c = (Cat) r;
```