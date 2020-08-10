---
title: Object class
date: 2020-08-10 05:08:92
category: java
draft: false
---

## Object class, toString()

모든 클래스의 root 클래스, 최상위 클래스(상속 관계에서)

Object class의 toString()은
1. 재정의를 안 했을 경우 -> 객체의 번지(address)를 출력한다.
2. 재정의를 했을 경우 -> 재정의된 메소드를 실행한다.


## Object class의 활용

다형성 인수

```java
public class ObjectTest {
    public static void main(String args) {
        display(new A());
        display(new B());
    }
    public void display(Object o) { // 다형성 인수
        if(o instanceof A) {
            ((A)o).go();
        } else {
            ((B)o).go();
        }
    }
}
```

다형성 배열

```java
public class ObjectTest {
    public static void main(String args) {
        Object[] o = new Object[2]; // 다형성 배열
        o[0] = new A();
        o[1] = new B();
        for(int i=0; i=o.length; i++) {
            if(o[i] instanceof A) {
                ((A)o[i]).go();
            } else {
                ((B)o[i]).go();
            }
        }
    }
}
```

downcasting, instanceof