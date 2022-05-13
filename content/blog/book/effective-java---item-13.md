---
title: Effective Java - Item 13
date: 2022-05-13 22:05:94
category: book
draft: false
---

## clone 재정의는 주의해서 진행하라

clone 메서드가 선언된 곳이 Cloneable이 아닌 Object이고, protected이다. 그래서 Cloneable을 구현하는 것만으로는 외부 객체에서 clone 메서드를 호출할 수 없다.

- clone 메서드를 잘 동작하게끔 해주는 구현 방법
- 언제 그렇게 해야하는지
- 가능한 다른 선택지

메서드 하나 없는 Cloneable 인터페이스는 무슨 일은 할까? -> Object의 protected 메서드인 clone의 동작 방식을 결정한다.

Cloneable을 구현한 클래스의 인스턴스에서 clone을 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환하며, 그렇지 않은 클래스의 인스턴스에서 호출하면 CloneNotSupportedException을 던진다. (상당히 이례적으로 사용한 예. 따라하기🙅🏻‍♂️)

쓸데없는 복사를 지양한다는 관점에서 보면 불변 클래스는 굳이 clone 메서드를 제공하지 않는 게 좋다.

재정의한 메서드의 반환 타입은 상위 클래스의 메서드가 반환하는 타입의 하위 타입일 수 있다. 이 방식으로 클라이언트가 형변화하지 않아도 되게끔 해주자.

```java
@Override
public PhoneNumber clone() {
    try {
        return (PhoneNumber) super.clone();
    } catch(CloneNotSupportedException e) {
        throw new AssertionError(); // 일어날 수 없는 일
    }
}
```

super.clone 호출을 try-catch 블록으로 감싼 이유 - Object의 clone 메서드가 검사 예외(checked exception)를 던지도록 선언되었기 때문이다. 거추장스러운 코드. -> 사실은 비검사 예외(unchecked exception)였어야 했다는 신호

가변 객체를 참조하는 클래스를 clone할 때 clone 메서드가 단순히 super.clone의 결과를 그대로 반환한다면? -> 원본이나 복제본 중 하나를 수정하면 다른 하나도 수정되어 불변식을 해칠 수 있다.

clone 메서드는 사실상 생성자와 같은 효과를 낸다. 즉 clone은 원본 객체에 아무런 해를 끼치지 않는 동시에 복제된 객체의 불변식을 보장해야 한다.

배열의 clone은 런타임 타입과 컴파일타임 타입 모두가 원본 배열과 똑같은 배열을 반환한다. 따라서 배열을 복제할 때는 배열의 clone 메서드를 사용하라고 권장한다.

직렬화와 마찬가지로 Cloneable 아키텍처는 '가변 객체를 참조하는 필드는 final로 선언하라'는 일반 용법과 충돌한다. 그래서 복제할 수 있는 클래스를 만들기 위해 일부 필드에서 final 한정자를 제거해야 할 수도 있다.

clone을 재귀적으로 호출하는 것이 충분하지 않을 때 - 해시테이블용 clone 메서드 -> 재귀 호출로 연결 리스트를 복제하는 방법은 그다지 좋지 않다. 재귀 호출 대신 반복자를 써서 순회하는 방향으로!

복잡한 가변 객체를 복제하는 마지막 방법 : super.clone을 호출하여 얻은 객체의 모든 필드를 초기 상태로 설정한 다음, 원본 객체의 상태를 다시 생성하는 고수준 메서드들을 호출한다.

저수준에서 바로 처리할 때보다는 느리다. Cloneable 아키텍처의 기초가 되는 필드 단위 객체 복사를 우회하기 때문에 전체 Cloneable 아키텍처와는 어울리지 않는 방식이기도 하다.

Object의 clone 메서드는 CloneNotSupportedException을 던진다고 선언했지만 재정의한 메서드는 그렇지 않다. public인 clone 메서드에서는 throws 절을 없애야 한다. 검사 예외를 던지지 않아야 그 메서드를 사용하기 편하기 때문이다.

상속해서 쓰기 위한 클래스 설계 방식 두 가지 중 어느 쪽에서든, 상속용 클래스는 Cloneable을 구현해서는 안 된다.

Cloneable을 구현한 스레드 안전 클래스를 작성할 때는 clone 메서드 역시 적절히 동기화해줘야 한다. Object의 clone 메서드는 동기화를 신경 쓰지 않았다. 그러니 super.clone 호출 외에 다른 할 일이 없더라도 clone을 재정의하고 동기화해줘야 한다.

Cloneable을 구현하는 모든 클래스는 clone을 재정의해야 한다. 이때 접근자는 public으로, 반환 타입은 클래스 자신으로 변경한다. 이 메서드는 가장 먼저 super.clone을 호출한 후 필요한 필드를 전부 적절히 수정한다.

Cloneable을 이미 구현한 클래스를 확장한다면 어쩔 수 없이 clone을 잘 작동하도록 구현해야 한다. 그렇지 않은 상황에서는 **복사 생성자와 복사 팩터리**라는 더 나은 객체 복사 방식을 제공할 수 있다.

```java
// 복사 생성자
public Yum(Yum yum) { ... };

// 복사 팩터리
public static Yum newInstance(Yum yum) { ... };
```

복사 생성자와 그 변형인 복사 팩터리는 Cloneable/clone 방식보다 나은 면이 많다.
- 언어 모순적이고 위험천만한 객체 생성 메커니즘(생성자를 쓰지 않는 방식)을 사용하지 않음
- 엉성하게 문서화된 규약에 기대지 않음
- 정상적인 final 필드 용법과도 충돌하지 않음
- 불필요한 검사 예외를 던지지 않음
- 형변환도 필요하지 않음

복자 생성자와 복사 팩터리는 해당 클래스가 구현한 '인터페이스' 타입의 인스턴스를 인수로 받을 수 있다.