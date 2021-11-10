---
title: Effective Java - Item 3
date: 2021-11-10 16:11:84
category: book
draft: false
---

## private 생성자나 열거 타입으로 싱글턴임을 보증하라

- 싱글턴(singleton)이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.
- 싱글턴의 예로는 함수와 같은 `무상태(stateless) 객체`나 `설계상 유일해야 하는 시스템 컴포넌트`를 들 수 있다.
- 싱글턴을 만드는 두 가지 방식이 있다. 두 방식 모두 생성자는 private으로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 publid static 멤버를 하나 마련해둔다.

### 방식 1 : public static 멤버가 final 필드인 방식
```java
// public static final 필드 방식의 싱글턴
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }

    public void leaveTheBuilding() { ... } 
}
```

- private 생성자느 public static final 필드인 `Elvis.INSTANCE`를 초기화할 때 딱 한 번만 호출된다.
- 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.
- 권한이 있는 클라이언트는 리플렉션 API인 `AccessibleObject.setAccessible`을 사용해 private 생성자를 호출할 수 있다. -> 이러한 공격을 방어하려면 생성자를 수정하여 두 번째 객체가 생성되려 할 때 예외를 던지게 하면 된다.

**장점**
- 해당 클래스가 싱글턴임이 API에 명백히 드러난다.
- 간결함


### 방식 2 : 정적 팩터리 메서드를 public static 멤버로 제공
```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }

    public void leaveTheBuilding() { ... }
}
```

- `Elvis.getInstance`는 항상 같은 객체의 참조를 반환하므로 제2의 Elvis 인스턴스란 결코 만들어지지 않는다.(리플렉션을 통한 예외는 똑같이 적용된다.)

**장점**
- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
- 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
- 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있다.
    - `Elvis::getInstance` -> `Supplier<Elvis>`
- 이러한 장점들이 굳이 필요하지 않다면 public 필드 방식이 좋다.

둘 중 하나의 방식으로 만든 싱글턴 클래스를 직렬화하려면 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하다. -> 모든 인스턴스를 일시적(transient)이라고 선언하고 `readResolve` 메서드를 제공해야 한다. 이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어진다.


### 방식 3 : 원소가 하나인 열거 타입을 선언

```java
public enum Elvis {
    INSTANCE;

    public void leaveTheBuilding() { ... }
}
```

- public 필드 방식과 비슷하지만, 더 간결하고, 추가 노력 없이 직렬화할 수 있다.
- 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 완벽히 막아준다.
- **대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.**
- 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.