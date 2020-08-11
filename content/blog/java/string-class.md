---
title: String class
date: 2020-08-11 12:08:75
category: java
draft: false
---

## String 클래스

자바에서   
- 문자열은 " " 쌍따옴표로 감싸면 된다.
- 문자열을 저장하는 기본 자료형은 없다.
- 문자열은 여러가지 조작을 할 수 있기 때문에 별도의 클래스로 자료형을 만들어 두었다.(java.lang.String)
- 문자열은 객체로 취급된다.

## 문자열 생성 방법

1. new로 생성

```java
// Heap area 에 메모리 생성
// str1와 str2 번지는 다르다
String str1 = new String("Apple");
String str2 = new String("Apple");
```

2. 문자열 상수로 생성

```java
// Literal Pool 에 메모리 생성 (재활용)
// str3와 str4의 번지는 같다 
String str3 = "Apple";
String str4 = "Apple";
```

> 문자열 비교는 **equals()** 사용