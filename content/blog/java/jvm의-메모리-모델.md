---
title: JVM의 메모리 모델
date: 2020-07-15 04:07:52
category: java
draft: false
---

JVM는 프로세서이기 때문에 동작을 위해 메모리가 필요하다.  
메모리를 구성하는 4가지 영역(종류) - Method Area, Stack Area, Heap Area, Literal Pool

### JVM이 실행클래스를 실행하는 절차(static 키워드가 붙은 경우)

1. 해당 클래스를 현재 디렉토리에서 찾는다 -> 성공 / 실패
실패 -> classpath 환경변수에 해당 클래스가 위치하는 폴더를 연결해주면 된다

2. 찾은 클래스를 메모리로 로딩한다. (컴파일된 기계어 코드를 메모리로 로딩)
static 키워드가 붙은 메소드를 메모리로 로딩한다
예) main 메소드를 호출하려면 메모리 위에 있어야 한다.

> Method Area : method 의 byte code가 저장되는 영역. static zone 과 non-static zone 두 가지 영역으로 나누어진다.  
static method는 Method Area의 static zone에 올라간다. 프로그램이 시작하기 전에 static 키워드를 가지고 있는 method들은 자동으로 static-zone으로 올라간다.

3. JVM은 static zone에서 main 메소드를 실행한다(호출, 시작)

어떤 메소드가 호출되면 호출 정보가 Stack Area로 들어간다(stack 영역을 할당받아서 저장된다)

> PC(프로그램 카운터)가 가리키고 있는 곳이 현재 프로그램이 동작되고 있는 위치

할당받은 stack 영역을 '지역'이라고 한다. 그래서 메소드에서 만들어지는 변수를 '지역변수'라고 지칭하는 것이다.

main에서 static-zone에 있는(메모리에 로딩된) 메소드만 호출할 수 있다.
다른 static method를 호출하면 해당 메소드가 stack으로 들어간다. PC가 변경된다. (프로그램 제어권이 넘어간다)

method가 끝나면 stack에서 빠지고 PC가 main을 가리킨다

4. main 이 끝나면 stack에서 빠진다. stack에 아무것도 없는 상태가 프로그램이 종료된 상태이다

static 키워드는 프로그램을 실행하기 전에 메서드의 기계어 코드를 자동으로 메모리에 로딩을 시키기 위해서 사용한다. static zone이라는 고정된 위치에 자동으로 로딩이 되게 만든다.

그렇다면 static 없을 때는 어떻게?? 따로 메모리에 로딩하는 방법이 있다.

### JVM이 실행클래스를 실행하는 절차(static 키워드 붙지 않은 경우)
static 키워드가 붙은 경우의 1, 2, 4과 같다

> Heap Area : 객체지향(class)프로그램을 만들 때 활용됨. 객체가 생성되는 메모리 영역(new 연산자 이용)

3. 객체를 생성해서 메모리에 올린다.
new 연산자를 이용해서 객체를 생성하면 Heap Area에 객체가 할당된다(영역이 만들어진다)
또한, method area의 non-static zone에 메소드 메모리가 할당된다.
Heap Area 할당된 객체는 mehotd area non-static zone에 할당된 메소드 메모리를 가리키는 pointer이다.