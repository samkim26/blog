---
title: (W1D4) Collection 이야기
date: 2021-08-05 15:08:00
category: devcourse
draft: false
---


## Collection

- 여러 데이터의 묶음을 의미한다.

  ![collection](./img/collection.png)

  

## Iterator

- 여러 데이터의 묶음을 풀어서 하나씩 처리할 수 있는 수단을 제공한다.

- next()를 통해서 다음 데이터를 조회할 수 있다.

- 역으로 움직일 수 없다. (이전 데이터를 조회할 수는 없다.)

  ```java
  while (iter.hasNext()) { // 순환
    String s = iter.next();
    int len = s.length();
    if (len % 2 == 0) continue;
    System.out.println(s);
  }
  ```

  



## Stream

- 데이터의 연속이다.
- Collection.stream()
- filter, map, forEach 같은 고차함수(함수를 인자로 받는 함수)를 제공한다.
- 동작 환경에 따라 각 스트림의 멀티스레드로 동작하거나 병렬적으로 동작해서 높은 성능을 보여준다.
- Stream 만드는 2가지 방법
  - generate()
  - iterate() : 첫 번째 인자(seed 값)이 두 번째 인자의 입력값으로 들어간다.

- 스트림을 사용하면 연속된 데이터에 대해서 풍부한 고차함수들을 사용하여 강력한 기능을 간결하게 표현할 수 있다. 
- 짧고 간결한 코드를 안정적으로 만들 수 있다.



## Optional

- NPE (Null Point Exception) : 가장 많이 발생하는 에러 중에 하나

- 자바에서는 (거의) 모든 것이 레퍼런스 => (거의) 모든 것이 null이 될 수 있다.

- 항상 null을 확인할 필요가 있다.

- (1) EMPTY 객체를 사용하는 방법

  - 아는 사람끼리만, 컨텍스트를 모르는 사람은 활용하거나 방어 코드를 사용할 수 없게 된다.

- (2) **Optional을 사용**한다.

  - Optional은 null이 될 수 있는 값들을 운반시켜준다.

    ```java
    Optional.empty()
    Optional.of( {DATA} )
    ```

  - 값 확인하는 방법

    ```java
    optionalUser.isEmpty(); // 값이 없으면 true
    optionalUser.isPresent(); // 값이 있으면 true
    ```

    ```java
    if (optionalUser.isPresent()) {
      // do 1
    } else {
      // do 2
    }
    
    if (optionalUser.isEmpty()) {
      // do 2
    } else {
      // do 1
    }
    
    optionalUser.ifPresentOrElse(user -> {
      // do 1
    }, () -> {
      // do 2
    });
    
    optionalUser.ifPresent(user -> {
      // do 1
    });
    ```

    