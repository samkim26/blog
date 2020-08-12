---
title: ArrayList, Wrapper class
date: 2020-08-12 14:08:72
category: java
draft: false
---

## ArrayList

배열 기본 동작을 클래스(API)로 만들어 놓음 -> IntArray

내가 만든 API : ObjectArray -> Java에서 제공해주는 API : ArrayList

ArrayList add() : Upcasting / get() : Downcasting


## Wrapper 클래스

기본 자료형을 객체 자료형으로 사용할 수 있도록 만들어 놓은 자료형(**포장 클래스**)

Integer, Float, Character, Boolean

int <-> Object 불가능 / Integer <-> Object 가능

```java
Integer a = 1; // Boxing

int b = new Integer(10); // Unboxing
```