---
title: Effective Java - Item 8
date: 2022-04-08 13:02:32
category: book
draft: false
---

## finalizer와 cleaner 사용을 피하라

자바는 두 가지 객체 소멸자를 제공한다.
- finalizer
  - **예측할 수 없고, 상황에 따라 위험할 수 있어 불필요하다.**
  - 오동작, 낮은 성능, 이식성 문제의 원인이 되기도 한다.
  - 기본적으로 '쓰지 말아야' 한다.
  - 자바 9에서는 finalizer를 사용 자제(deprecated) API로 지정하고 cleaner를 대안으로 소개했다.
- cleaner
  - **finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고, 느리고, 일반적으로 불필요하다.**

자바의 finalizer와 cleaner는 C++의 파괴자(destructor)와는 다른 개념이다.
- C++에서의 파괴자는 (생성자의 꼭 필요한 대척점으로) 특정 객체와 관련된 자원을 회수하는 보편적인 방법이다. 자바에서는 접근할 수 없게 된 객체를 회수하는 역할을 가비지 컬렉터가 담당하고, 프로그래머에게는 아무런 작업도 요구하지 않는다.
- C++ 파괴자는 비메모리 자원을 회수하는 용도로도 쓰인다. 자바에서는 try-with-resources와 try-finally를 사용해 해결한다.

finalizer와 cleaner는 즉시 수행된다는 보장이 없다. 
- 객체에 접근할 수 없게 된 후 finalizer와 cleaner가 실행되기까지 얼마나 걸릴지 알 수 없다. 
- -> **finalizer와 cleaner로는 제때 실행되어야 하는 작업은 절대 할 수 없다.**
- 예) 파일 닫기를 finalizer와 cleaner에 맡기면 중대한 오류를 일으킬 수 있다. 시스템이 동시에 열 수 있는 파일의 개수에 한계가 있기 때문.

finalizer나 cleaner를 얼마나 신속히 수행할지는 전적으로 가비지 컬렉터 알고리즘에 달렸으며, 이는 가비지 컬렉터 구현마다 천차만별이다. finalizer나 cleaner 수행 시점에 의존하는 프로그램의 동작 또한 마찬가지다.

굼뜬 finalizer는 현업에서도 실제로 문제를 일으킨다.
- 클래스에 finalizer를 달아두면 그 인스턴스의 자원 회수가 제멋대로 지연될 수 있다.
- 자바 언어 명세는 어떤 스레드가 finalizer를 수행할지 명시하지 않으니 이 문제를 예병할 보편적인 해법은 없다. 사용하지 않는 방법 뿐이다. 
- cleaner는 자신을 수행할 스레드를 제어할 수 있다는 면에서 조금 낫다. 하지만 여전히 백그라운드에서 수행되며 가비지 컬렉터의 통제하에 있으니 즉각 수행되리라는 보장은 없다.

자바 언어 명세는 finalizer나 cleaner의 수행 시점뿐 아니라 수행 여부조차 보장하지 않는다. 접근할 수 없는 일부 객체에 딸린 종료 작업을 전혀 수행하지 못한 채 프로그램이 중단될 수도 있다는 얘기다. -> 프로그램 생애주기와 상관 없는, **상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안된다.**
예) 데이터베이스 같은 공유 자원의 영구 락(lock) 해제

finalizer 동작 중 발생한 예외는 무시되며, 처리할 작업이 남았더라도 그 순간 종료된다. 잡지 못한 예외 때문에 해당 객체는 자칫 마무리가 덜 된 상태로 남을 수 있다. 그리고 다른 스레드가 이처럼 훼손된 객체를 사용하려 한다면 어떻게 동작할지 예측할 수 없다. 그나마 cleaner를 사용하는 라이브러리는 자신의 스레드를 통제하기 때문에 이러한 문제가 발생하지 않는다.

**finalizer와 cleaner는 심각한 성능 문제도 동반한다.**
- 저자 컴퓨터에서 AutoCloseable 객체를 생성하고 가비지 컬렉터가 수거하기까지 12ns
- finalizer를 사용하면 550ns
- -> finalizer가 가비지 컬렉터의 효율을 떨어뜨리기 때문
- cleaner도 클래스의 모든 인스턴스를 수거하는 형태로 사용하면 성능은 finalizer와 비슷하다.

**finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수도 있다.**
- finalizer 공격 원리
  - 생성자나 직렬화 과정에서 예외가 발생하면, 이 생성되다 만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 된다.
  - 이 finalizer는 정적 필드에 자신의 참조를 할당하여 가비지 컬렉터가 수집하지 못하게 막을 수 있다.
  - **객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만, finalizer가 있다면 그렇지도 않다.**
- final 클래스들은 그 누구도 하위 클래스를 만들 수 없으니 이 공격에서 안전하다.
- **final이 아닌 클래스를 finalizer 공격으로 방어하려면 아무 일도 하지 않는 finalize 메서드를 만들고 final로 선언하자.**

**finalizer나 cleaner를 대신해줄 묘안**

AutoCloseable을 구현해주고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출하면 된다. (일반적으로 예외가 발생해도 제대로 종료되도록 try-with-resources를 사용해야 한다.)

각 인스턴스는 자신이 닫혔는지를 추적하는 것이 좋다. close 메서드에서 이 객체는 더 이상 유효하지 않음을 필드에 기록하고, 다른 메서드는 이 필드를 검사해서 객체가 닫힌 후에 불렸다면 IllegalStateException을 던지는 것이다.

finalizer와 cleaner의 적절한 쓰임새가 두 가지 있다.
- 자원의 소유자가 close 메서드를 호출하지 않는 것에 대비한 안정망 역할
  - cleaner나 finalizer가 즉시(혹은 끝까지) 호출되리라는 보장은 없지만, 클라이언트가 하지 않은 자원 회수를 늦게라도 해주는 것이 아예 안하는 것보다는 낫다.
  - 이런 안정망 역할의 finalizer를 작성할 때는 그럴만한 값어치가 있는지 심사숙고하자.
  - FileInputStream, FileOutputStream, ThreadPoolExecutor
- 네이티브 피어(native peer)와 연결된 객체에서의 역할
  > 네이티브 피어란 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체를 말한다.
  - 네이티브 피어는 자바 객체가 아니니 가비지 컬렉터는 그 존재를 알지 못한다.
  - 그 결과 자바 피어를 회수할 때 네이티브 객체까지 회수하지 못한다.
  - cleaner나 finalizer가 나서서 처리하기에 적당한 작업이다.
  - 단, 성능 저하를 감당할 수 있고 네이티브 피어가 심각한 자원을 가지고 있지 않을 때에만 해당된다.
  - 성능 저하를 감당할 수 없거나 네이티브 피어가 자원을 즉시 회수해야 한다면 close 메서드를 사용해야 한다.

### cleaner 사용

cleaner를 사용하지 말지는 순전히 내부 구현 방식에 관한 문제다. 즉, finalizer와 달리 cleaner는 클래스의 public API에 나타나지 않는다.

- cleaner를 안전망으로 활용하는 AutoCloseable 클래스
```java
public class Room implements AutoCloseable {

    private static final Cleaner cleaner = Cleaner.create();

    // 청소가 필요한 자원. 절대 Room을 참조해서는 안 된다.
    private static class State implements Runnable {

        int numJunkPiles;

        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }

        // close 메서드나 cleaner가 호출한다.
        @Override
        public void run() {
            System.out.println("방 청소");
            numJunkPiles = 0;
        }
    }

    // 방 상태
    private final State state;

    private final Cleaner.Cleanable cleanable;

    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }

    @Override
    public void close() {
        cleanable.clean();
    }
}
```

- static으로 선언된 중첩 클래스인 State는 cleaner가 방을 청소할 때 수거할 자원들을 담고 있다.
  - 단순히 방 안의 쓰레기 수를 뜻하는 numJunkPiles 필드가 수거할 자원에 해당한다.
  - 더 현실적으로 만들려면 이 필드는 네이티브 피어를 가리키는 포인터를 담은  final long 변수여야 한다.
- State는 Runnable을 구현하고, 그 안의 run 메서드는 cleanable에 의해 딱 한 번만 호출될 것이다.
  - cleanble 객체는 Room 생성자에서 cleaner에 Room과 State를 등록할 때 얻는다.
- run 메서드가 호출되는 상황은 둘 중 하나다.
  - 보통은 Room의 close 메서드를 호출할 때 - close 메서드에서 Cleanable이 clean을 호출하면 이 메서드 안에서 run을 호출한다.
  - 가비지 컬렉터가 Room을 회수할 때까지 클라이언트가 close를 호출하지 않는다면, cleaner가 (바라건대) State의 run 메서드를 호출

State 인스턴스는 '절대로' Room 인스턴스를 참조해서는 안 된다. Room 인스턴스를 참조할 경우 순환참조가 생겨 가비지 컬렉터가 Room 인스턴스를 회수해갈 (따라서 자동 청소될) 기회가 오지 않는다. -> State가 정적 중첩 클래스인 이유
정적이 아닌 중첩 클래스는 자동으로 바깥 객체의 참조를 갖게 되기 때문이다. 이와 비슷하게 람다 역시 바깥 객체의 참조를 갖기 쉬우니 사용하지 않는 것이 좋다.

Room의 cleaner는 단지 안전망으로 쓰였다. 클라이언트가 모든 Room 생성을 try-with-resources 블록으로 감쌌다면 자동 청소는 전혀 필요하지 않다.

```java
public class Adult {
    
    public static void main(String[] args) {
        try (Room myRoom = new Room(7)) {
            System.out.println("안녕~");
        }
    }
}
```

- 방 청소를 하지 않는 프로그램
```java
public class Teenager {

    public static void main(String[] args) {
        new Room(99);
        System.out.println("아무렴");
    }
}
```
-> "방 청소"는 한 번도 출력되지 않는다. - 예측할 수 없다고 한 상황(청소가 이뤄질지는 보장X)

### 핵심 정리

cleaner(자바 8까지는 finalizer)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.