---
title: Effective Java - Item 20
date: 2022-06-02 21:11:32
category: book
draft: false
---

## 추상 클래스보다는 인터페이스를 우선하라

자바가 제공하는 다중 구현 메커니즘 : **인터페이스**, **추상 클래스**

자바 8부터 인터페이스도 디폴트 메서드(default method)를 제공할 수 있게 되어, 이제는 두 메커니즘 모두 인스턴스 메서드를 구현 형태로 제공할 수 있다.

둘의 가장 큰 차이는 추상 클래스가 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다는 점이다.

자바는 단일 상속만 지원 -> 추상 클래스 방식은 새로운 타입을 정의하는 데 커다란 제약을 안게 된다.

인터페이스가 선언한 메서드를 모두 정의하고 그 일반 규약을 잘 지킨 클래스라면 다른 어떤 클래스를 상속했든 같은 타입으로 취급된다.

**기존 클래스에도 손쉽게 새로운 인터페이스를 구현해넣을 수 있다.** 인터페이스가 요구하는 메서드를 (아직 없다면) 추가하고, 클래스 선언에 implements 구문만 추가하면 끝이다. 반면에, 기존 클래스 위에 새로운 추상 클래스를 끼워넣기는 어려운 게 일반적이다.

인터페이스는 믹스인(mixin) 정의에 안성맞춤이다.
> 믹스인이란 클래스가 구현할 수 있는 타입으로, 믹스인을 구현한 클래스에 원래의 '주된 타입' 외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 준다.
> 대상 타입의 주된 기능에 선택적 기능을 '혼합(mixed in)'한다고 해서 믹스인이라 부른다.

추상 클래스로는 믹스인을 정의할 수 없다. 기존 클래스에 덧씌울 수 없기 때문이다.

**인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있다.**

거대한 클래스 계층구조에는 공통 기능을 정의해놓은 타입이 없으니, 자칫 매개변수 타입만 다른 메서드들을 수없이 많이 가진 거대한 클래스를 낳을 수 있다.

래퍼 클래스 관용구와 함께 사용하면 **인터페이스는 기능을 향상시크는 안전하고 강력한 수단이 된다.** 타입을 추상 클래스로 정의해두면 그 타입에 기능을 추가하는 방법은 상속뿐이다.(상속해서 만든 클래스는 래퍼 클래스보다 활용도가 떨어지고 깨지기는 더 쉽다.)

인터페이스의 메서드 중 구현 방법이 명백한 것이 있다면, 그 구현을 디폴트 메서드로 제공해 프로그래머들의 일감을 덜어줄 수 있다.

디폴트 메서드에도 제약은 있다.
- equals, hashCode 같은 Object의 메서드들은 디폴트 메서드로 제공해서는 안 된다.
- 인터페이스는 인스턴스 필드를 가질 수 없고 public이 아닌 정적 멤버도 가질 수 없다. (private 정적 메서드 제외)
- 여러분이 만들지 않은 인터페이스에는 디폴트 메서드를 추가할 수 없다.

인터페이스와 추상 골격 구현(skeletal implementation) 클래스를 함께 제공하는 식으로 인터페이스와 추상 클래스의 장점을 모두 취하는 방법도 있다.
- 인터페이스로는 타입을 정의하고, 필요하면 디폴트 메서드 몇 개도 함께 제공한다.
- 골격 구현 클래스는 나머지 메서드들까지 구현한다.
- 골격 구현을 확장하는 것만으로 인터페이스를 구현하는 데 필요한 일이 대부분 완료된다. -> 템플릿 메서드 패턴

제대로 설계했다면 골격 구현은 (독립된 추상 클래스든 디폴트 메서드로 이뤄진 인터페이스든) 그 인터페이스로 나름의 구현을 만들려는 프로그래머의 일을 상당히 덜어준다.

```java
static List<Integer> intArrayAsList(int[] a) {
    Object.requireNonNull(a);

    // 다이아몬드 연산자(<>) 사용 자바 9부터
    return new AbstractList<>() {
        @Override
        public Integer get(int i) {
            return a[i];
        }

        @Override
        public Integer set(int i, Integer val) {
            int oldVal = a[i];
            a[i] = val;
            return oldVal;
        }

        @Override
        public int size() {
            return a.length;
        }
    }
}
```

골격 구현 클래스의 아름다움은 추상 클래스처럼 구현을 도와주는 동시에, 추상 클래스로 타입을 정의할 때 따라오는 심각한 제약에서는 자유롭다는 점에 있다.

구조상 골격 구현을 확장하지 못하는 처지라면 인터페이스를 직접 구현해야 한다. 이런 경우라도 인터페이스가 직접 제공하는 디폴트 메서드의 이점을 여전히 누릴 수 있다.

```java
// 골격 구현 클래스
public abstract class AbstractMapEntry<K, V> implements Map.Entry<K, V> {

    // 변경 가능한 엔트리는 이 메서드를 반드시 재정의해야 한다.
    @Override
    public V setValue(V value) {
        throw new UnsupportedOperationException();
    }

    // Map.Entry.equals의 일반 규약을 구현한다.
    @Override
    public boolean equals(Object o) {
        if (o == this) {
            return true;
        }

        if (!(o instanceof Map.Entry)) {
            return false;
        }

        Map.Entry<?, ?> e = (Map.Entry) o;
        return Objects.equals(e.getKey(), getKey())
            && Objects.equals(e.getValue(), getValue());
    }

    // Map.Entry.hashCode의 일반 규약을 구현한다.
    @Override
    public int hashCode() {
        return Objects.hashCode(getKey()) ^ Objects.hashCode(getValue());
    }

    @Override
    public String toString() {
        return getKey() + "=" + getValue();
    }
}
```

골격 구현은 기본적으로 상속해서 사용하는 걸 가정한다. -> 아이템 19 설계 및 문서화 지침 모두 따라야 한다.

단순 구현(simple implementation)은 골격 구현의 작은 변종으로, AbstractMap.SimpleEntry가 좋은 예다.

단순 구현도 골격 구현과 같이 상속을 위해 인터페이스를 구현한 것이지만, 추상 클래스가 아니란 점이 다르다.


### 핵심정리

- 다중 구현용 타입으로는 인터페이스가 가장 적합하다.
- 복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 고려해보자.
- 골격 구현은 '가능한 한' 인터페이스의 디폴트 메서드로 제공하며 그 인터페이스를 구현한 모든 곳에서 활용하도록 하는 것이 좋다.