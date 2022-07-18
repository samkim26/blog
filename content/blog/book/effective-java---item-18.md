---
title: Effective Java - Item 18
date: 2022-05-27 10:20:21
category: book
draft: false
---

## 상속보다는 컴포지션을 사용하라

상속은 코드를 재사용하는 강력한 수단이지만, 항상 최선은 아니다. 잘못 사용하면 오류를 내기 쉬운 소프트웨어를 만들게 된다.

일반적인 구체 클래스를 패키지 경계를 넘어, 즉 다른 패키지의 구체 클래스를 상속하는 일은 위험하다.

**메서드 호출과 달리 상속은 캡슐화를 깨뜨린다.** 다르게 말하면, 상위 클래스가 어떻게 구현되느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있다.

다음 릴리스에서 상위 클래스에 새로운 메서드를 추가한다면 하위 클래스가 깨지기 쉽다. 하위 클래스에서 재정의하지 못한 그 새로운 메서드를 사용해 '허용되지 않은' 원소를 추가할 수 있게 된다.

`메서드 재정의`가 문제의 원인

클래스를 확장하더라도 메서드를 재정의하는 대신 새로운 메서드를 추가하면 괜찮지 않을까? -> 훨씬 안전한 것은 맞지만, 위험이 전혀 없는 것은 아니다.

다음 릴리스에서 상위 클래스에 새 메서드가 추가됐는데, 운 없게도 하필 하위 클래스에 추가한 메서드와 시그니처가 같고 반환 타입은 다르다면 하위 클래스는 컴파일조차 되지 않는다. 반환 타입마저 같다면 상위 클래스의 새 메서드를 재정의한 꼴이된다.

문제를 모두 피해 가는 묘안 -> **기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조**

**기존 클래스가 새로운 클래스의 구성요소로 쓰인다는 뜻에서 이러한 설계를 컴포지션(composition; 구성)이라 한다.**

새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 심지어 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않는다.

- 래퍼 클래스 - 상속 대신 컴포지션 사용
  ```java
  public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;

    public InstrumentedSet(Set<E> s) {
      super(s);
    }

    @Override
    public boolean add(E e) {
      addCount++;
      return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
      addCount += c.size();
      return super.addAll(c);
    }

    public int getAddCount() {
      return addCount;
    }
  }
  ```

- 재사용할 수 있는 전달 클래스
  ```java
  public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    
    public ForwardingSet(Set<E> s) {
      this.s = s;
    }

    public void clear() { s.clear(); }
    public boolean contains(Object o) { return s.contains(o); }
    public boolean isEmpty() { return s.isEmpty(); }
    public int size() { return s.size(); }
    public Iterator<E> iterator() { return s.iterator(); }
    public boolean add(E e) { s.add(e); }
    public boolean remove(Object o) { s.remove(o); }
    public boolean containsAll(Collection<?> c) { return s.containsAll(c); }
    public boolean addAll(Collection<? extends E> c) { return s.addAll(c); }
    public boolean removeAll(Collection<?> c) { return s.removeAll(c); }
    public boolean retainAll(Collection<?> c) { return s.retainAll(c); }
    public Object[] toArray() { return s.toArray(); }
    public <T> T[] toArray(T[] a) { return s.toArray(a); }
    @Override public boolean equals(Object o) { reutrn s.equals(o); }
    @Override public int hashCode() { return s.hashCode(); }
    @Override public String toString() { return s.toString(); }
  }
  ```

-> 임의의 Set에 계측 기능을 덧씌워 새로운 Set으로 만듬. 어떠한 Set 구현체라도 계측할 수 있으며, 기존 생성자들과도 함께 사용할 수 있다.

InstrumentedSet 같은 클래스를 래퍼 클래스라 하며, 다른 Set에 계측 기능을 덧씌운다는 뜻에서 데코레이터 패턴(Decorator pattern)이라고 한다. 컴포지션과 전달의 조합은 넓은 의미로 위임(delegation)이라고 부른다. 엄밀히 따지면 래퍼 객체가 내부 객체에 자기 자신의 참조를 넘기는 경우만 위임에 해당한다.

래퍼 클래스는 단점이 거의 없다. - 래퍼 클래스가 콜백(callback) 프레임워크와는 어울리지 않는 다는 점만 주의!

전달 메서드가 성능에 주는 영향이나 래퍼 객체가 메모리 사용량에 주는 영향을 걱정하는 사람도 있지만, 실전에서는 둘 다 별다른 영향이 없다고 밝혀졌다.

**상속은 반드시 하위 클래스가 상위 클래스의 '진짜' 하위 타입인 상황에서만 쓰여야 한다. 다르게 말하면, 클래스 B가 클래스 A와 is-a 관계일 때만 클래스 A를 상속해야 한다.**

컴포지션을 써야 할 상황에서 상속을 사용하는 건 내부 구현을 불필요하게 노출하는 꼴이다. 그 결과 API가 내부 구현에 묶이고 그 클래스의 성능도 영원히 제한된다. 더 심각한 문제는 클라이언트가 노출된 내부에 직접 접근할 수 있다는 점이다.

컴포지션 대신 상속을 사용하기로 결정하기 전에 마지막으로 자문해야 할 질문
- 확장하려는 클래스의 API에 아무런 결함이 없는가?
- 결함이 있다면, 이 결함이 클래스의 API까지 전파돼도 괜찮은가?


### 핵심 정리

- 상속은 강력하지만 캡슐화를 해친다는 문제가 있다.
- 상속은 상위 클래스와 하위 클래스가 순수한 is-a 관계일 때만 써야 한다.
- 상속의 취약점을 피하려면 상속 대신 컴포지션과 전달을 사용하자.
- 래퍼 클래스는 하위 클래스보다 견고하고 강력하다.