---
title: Object
date: 2021-08-02 22:08:85
category: java
draft: false
---

# Object

- 모든 객체의 최상위 객체
- 모든 객체에는 Object의 메소드를 호출할 수가 있다.
- 모든 객체는 Object 클래스의 일부 메소드를 override해서 사용할 수도 있다.

# Object 객체의 메소드와 기능

## toString()

- 기본 동작 : 객체의 해시코드 출력
- toString() 원형 : 인스턴스 객체의 클래스 이름, '@'기호, 그리고 16진수로 표현된 객체의 해시 코드로 구성된 문자열을 리턴
    ```java
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
    ```
- 객체의 정보를 문자열 형태로 표현하고자 할 때 override 한다.


## equals()

- 객체 간에 동등한지 여부를 나타낸다.
- 두 객체의 값이 같은지 확인한다. (객체끼리 내용을 비교)
    > == 비교 연산자 : 주소의 값을 비교한다.

## hashCode()

- 객체의 해시코드 값을 반환한다.
  > 해시코드 : 객체를 식별할 수 있는 유니크한 값
- HashMap에서 제공하는 것과 같은 Hash Table의 이점을 위해 지원된다.


### 참고

- https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html