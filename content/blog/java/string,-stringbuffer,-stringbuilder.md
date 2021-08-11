---
title: String, StringBuffer, StringBuilder
date: 2021-08-02 22:08:79
category: java
draft: false
---
## String

### Java에서는 String을 특별하게 취급한다. -> **매우 빈번하게 사용되기 때문**

- String 리터럴

  - 공통 pool(Constant Pool)에 저장이 된다.
  - 저장 공간을 아끼기 위해서 같은 내용의 문자열은 **공유**한다.

- String 객체

  - 힙 영역에 저장이 된다.

    > 원시형(Primivive)은 스택 영역에 저장된다.

  - 같은 내용의 문자열이라도 공유를 하지 않는다.

### String에 대해서는 +연산을 사용하지 말자.

- String은 **불변 객체**이다. (수정이 불가하다)
- +연산을 하게 된다면 내용이 수정되는 것이 아니라 완전히 새로운 String 객체를 생성해 반환한다.
  - 기존 영역은 Garbage로 남아있다가 Garbage Collection에 의해 사라지게 된다.
  - 때문에 +연산을 자주하게 되면 비효율적이다.

## StringBuffer, StringBuilder

- StringBuffer, StringBuilder는 **가변 객체**이다.
- 동일한 객체 내에서 문자열을 변경하는 것이 가능하다.

### StringBuffer VS StringBuilder

- 동기화의 유무가 가장 큰 차이점이다.

  - StringBuffer : 멀티스레드 환경에서 안전하다. 메소드는 필요한 경우 동기화된다. 

    > 동기화(synchronized) : 다른 스레드에서 현재 실행중인 메소드나 블록이 끝날 때까지 대기해야함을 의미

  - StringBuilder : 동기화를 지원하지 않는다. 단일스레드에서 성능이 StringBuffer보다 뛰어나다.

    - 싱글스레드 환경에서 시간 측정

      ```java
      // 싱글스레드
      StringBuffer stringBuffer = new StringBuffer();
      StringBuilder stringBuilder = new StringBuilder();
      
      new Thread(() -> {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000000; i++) {
          stringBuffer.append(i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("StringBuffer 시간(milliseconds) : " + (endTime - startTime));
      }).start();
      
      new Thread(() -> {
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000000; i++) {
          stringBuilder.append(i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("StringBuilder 시간(milliseconds) : " + (endTime - startTime));
      }).start();
      ```

      싱글스레드 환경에서 StringBuilder가 StringBuffer보다 대체적으로 빠르다는 결과를 위 코드를 통해 확인할 수 있다.

------

### 추가 개념

- Constant Pool : Integer, String 같은 레퍼런스 타입의 데이터 값 또는 메소드 호출 Class 호출 등을 저장하는 JVM의 메모리 공간 중 하나(힙 영역 내)

- 힙 영역, 스택 영역

  - 힙 영역 : 객체가 생성되는 메모리 영역(new 연산자 이용), 사용자에 의해 메모리 공간이 동적으로 할당되고 해제됨
  - 스택 영역 : 함수의 호출과 관계되는 지역 변수와 매개변수가 저장되는 영역

- Gargabe Collection : JVM의 Garbage Collector가 유효하지 않은 메모리(불필요한 메모리)를 알아서 정리해주는 것

- 단일스레드, 멀티스레드

  - 단일스레드 : 하나의 프로세스에서 하나의 스레드 실행
  - 멀티스레드 : 하나의 프로세스 내에서 둘 이상의 스레드가 동시에 작업을 수행

  > 프로세스 : 운영체제로부터 자원을 할당받는 작업의 단위
  >
  > 스레드 : 프로세스가 할당받은 자원을 이용하는 실행의 단위

---

### 참고

- https://ifuwanna.tistory.com/221
- https://pjh3749.tistory.com/255
- https://docs.oracle.com/javase/8/docs/api/java/lang/StringBuilder.html
- https://docs.oracle.com/javase/8/docs/api/java/lang/StringBuffer.html