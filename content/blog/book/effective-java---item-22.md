---
title: Effective Java - Item 22
date: 2022-06-05 21:05:40
category: book
draft: false
---

## 인터페이스는 타입을 정의하는 용도로만 사용하라

인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 있는 타입 역할을 한다.  
클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에게 얘기해주는 것이다. 인터페이스는 오직 이 용도로만 사용해야 한다.

맞지 않는 예) 상수 인터페이스 : 메서드 없이, 상수를 뜻하는 static final 필드로만 가득 찬 인터페이스
```java
public interface PhysicalConstants {
    
    static final double AVOGADROS_NUMBER = 6.022_140_857e23;

    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;

    static final double ELETRON_MASS = 9.109_383_56e-31;
}
```

**상수 인터페이스 안티패턴은 인터페이스를 잘못 사용한 예다.** 클래스 내부에서 사용하는 상수는 외부 인터페이스가 아니라 내부 구현에 해당한다.

상수를 공개할 목적이라면 더 합당한 선택지가 몇 가지 있다. 특정 클래스나 인터페이스와 강하게 연관된 상수라면 그 클래스나 인터페이스 자체에 추가해야 한다.
  - 예) 모든 숫자 기본 타입의 박싱 클래스
  - 열거 타입
  - 인스턴스화할 수 없는 유틸리티 클래스
    ```java
    public class PhysicalConstants {
        private PhysicalConstants() { } // 인스턴스화 방지

        // 숫자 리터럴에 사용한 밑줄(_) : 자바 7부터 허용. 읽기 편하게 해줌.
        public static final double AVOGADROS_NUMBER = 6.022_140_857e23;

        public static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;

        public static final double ELETRON_MASS = 9.109_383_56e-31;
    }
    ```