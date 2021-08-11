## Object

- 모든 객체의 최상위 객체
- 모든 객체에는 Object의 메소드를 호출할 수가 있다.
- 모든 객체는 Object 클래스의 일부 메소드를 override해서 사용할 수도 있다.



## Object 객체의 메소드와 기능

### toString()

- 기본 동작 : 객체의 해시코드 출력

- toString() 원형 : 인스턴스 객체의 클래스 이름, '@'기호, 16진수로 표현된 객체의 해시 코드로 구성된 문자열을 리턴

  ```java
  public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
  }
  ```

- 객체의 정보를 문자열 형태로 표현하고자 할 때 override 한다.

  - 가능하다면 toString 메소드는 객체 내의 중요 정보를 전부 담아 반환해야 한다.
  - toString이 반환하는 문자열의 형식을 명시하건 그렇지 않건 간에, 어떤 의도인지는 문서에 분명하게 남겨야 한다.
  - toString이 반환하는 문자열에 포함되는 정보들은 전부 프로그래밍을 통해서 가져올 수 있도록 하라.
    - toString 반환값을 파싱해서 쓰는 일이 없도록 한다. toString 구현이 바뀌면 그런 코드는 다 못 쓰게 된다.

### equals()

- 객체 간에 동등한지 여부를 나타낸다.

- 동등성 비교 : 객체 내부의 값을 비교한다. 

  > 동일성 비교 == : 참조값(객체의 주소값) 비교한다.

- String 클래스

  ```java
  String a = "hello";
  String b = "hello";
  System.out.println(a == b); // true
  System.out.println(a.equals(b)); // true
  ```

  위 코드에서  `a` 와 `b` 는  constant pool의 같은 문자열을 참조하기 때문에 동일성, 동등성 비교 모두 true를 반환한다.

  ```java
  String c = new String("hi");
  String d = new String("hi");
  System.out.println(c == d); // false
  System.out.println(c.equals(d)); // true
  ```

  위 코드에서 `c` 와 `d` 는 Heap 영역의 각각 다른 문자열의 인스턴스를 참조하기 때문에 동일성 비교 시 false를 반환하지만, 동등성 비교 시 두 문자열의 값은 같기 때문에 true를 반환한다.

- String 클래스가 아닌 개발자가 생성한 클래스의 객체는 equals() 메소드를 오버라이드해서 써야한다.

### hashCode()

- 객체의 해시코드 값을 반환한다.

  > 해시코드 : 객체를 식별할 수 있는 유니크한 값

- **객체의 메모리 번지**를 이용해서 해시코드를 만들어 리턴하기 때문에 객체마다 다른 값을 가지고 있다.

- hashCode()는 데이터가 저장되는 위치를 결정하기 위해 사용된다.
  - Collection 프레임워크에서 HashSet, HashMap, HashTable은 해시코드를 이용한다.
  - 인스턴스의 hashCode() 결과가 같다면 동일한 key로 간주한다.
  - key의 해시코드를 통해 value를 더 쉽게 찾아낼 수 있다.

- equals()를 오버라이딩한 클래스에서는 hashCode()도 오버라이딩 해야한다.
  - equals()를 통해 두 개의 객체가 같다고 판단했다면, 두 객체는 똑같은 해시코드 값을 반환해야 한다.
  - hashCode()를 오버라이딩 하지 않으면 같은 객체라도 해시코드가 다를 수 있다.

- HashSet, HashMap, HashTable 의 동등 비교

  - hashCode() 메소드를 실행해서 리턴된 해시코드가 같은지 확인한다.
    - 해시코드 값이 다르면 다른 객체로 판단한다.
    - 해시코드 값이 같으면 equal() 메소드로 다시 비교한다.
  - 두 단계를 통과해야 동등 객체로 판단한다.

- String 객체의 해시코드는 아래와 같이 계산된다.

  ```mathematica
  s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]
  ```

---

## 참고

- https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html