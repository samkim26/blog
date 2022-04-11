---
title: Effective Java - Item 10
date: 2022-04-11 17:10:32
category: book
draft: false
---

## equals는 일반 규약을 지켜 재정의하라

equals 메서드는 재정의하기 쉬워 보이지만 자칫하면 끔찍한 결과를 초래한다.  

문제를 회피하는 가장 쉬운 길 -> 아예 재정의하지 않는 것

아래 열거한 상황 중 하나에 해당한다면 재정의하지 않는 것이 최선이다.

- 각 인스턴스가 본질적으로 고유하다.
  - 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스 - Thread
- 인스턴스의 '논리적 동치성(logical equality)'을 검사할 일이 없다.
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
  - 대부분의 Set 구현체는 AbstractSet이 구현한 equals를 상속, List 구현체들은 AbstractList, Map 구현체들은 AbstractMap으로부터 상속
- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.
  - 실수로라도 호출되는 걸 막고 싶다면?
  ```java
  @Override
  public boolean equals(Object o) {
    throw new AssertError(); // 호출 금지!
  }
  ``` 

재정의 해야할 때는?
- 객체 식별성(object identity; 두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때
  - 주로 값 클래스들 - Integer, String
  - equals가 논리적 동치성을 확인하도록 재정의해두면, 값 비교 뿐만 아니라 Map의 키와 Set의 원소로 사용할 수 있게 된다.

값 클래스라 해도, 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는 인스턴스 통제 클래스라면 equals를 재정의하지 않아도 된다. - Enum이 여기 해당한다. -> 논리적 동치성과 객체 식별성이 사실상 똑같은 의미가 된다.

Object 명세에 적힌 규약 - equals 메서드는 동치관계(equivalence relation)을 구현하며, 다음을 만족한다.
- 반사성(reflexivity) : null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true다.
- 대칭성(symmetry) : null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)가 true면 y.equals(x)도 true다.
- 추이성(transitivity) : null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y)가 true이고, y.equals(z)도 true면 x.equals(z)도 true다.
- 일관성(coinsistency) : null이 아닌 모든 참조 값 x, y에 대해, x.eqauls(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.
- null-아님 : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다.

Object 명세에서 말하는 동치관계란? 집합을 서로 같은 원소들로 이뤄진 부분집합으로 나누는 연산이다. 이 부분집합을 동치류(equivalence clas; 동치 클래스)라 한다. equals 메서드가 쓸모 있으려면 모든 원소가 같은 동치류에 속한 어떤 원소와도 서로 교환할 수 있어야 한다.

양질의 equals 메서드 구현 방법
1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다. 자기 자신이면 true 반환 -> 단순한 성능 최적화용
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다. 그렇지 않다면 false 반환
3. 입력을 올바른 타입으로 형변환한다.
4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다. 모든 필드가 일치하면 true, 하나라도 다르면 false 반환

- float과 double을 제외한 기본 타입 필드는 == 연산자로 비교
- 참조 타입 필드는 각각의 equals 메서드
- float과 double 필드는 각각 정적 메서드인 Float.compare(float, float)와 Double.compare(double, double)로 비교
  - 특별 취급하는 이유는 특정한 부동소수 값을 다뤄야 하기 때문
  - Float.equals와 Double.equals 메서드를 대신 사용할 수도 있지만, 오토박싱을 수반할 수 있으니 성능상 좋지 않다.

떄론 null도 정상 값으로 취급하는 참조 필드도 있다. 이런 필드는 정적 메서드인 Objects.equals(Object, Object)로 비교해 NullPointerException 발생을 예방하자.

어떤 필드를 먼저 비교하느냐가 equals의 성능을 좌우하기도 한다. 최상의 성능을 바란다면 다를 가능성이 더 크거나 비교하는 비용이 싼 (혹은 둘 다 해당하는)필드를 먼저 비교하자.

동기화용 락(lock) 필드 같이 객체의 논리적 상태와 관련 없는 필드는 비교하면 안 된다.

핵심 필드로부터 계산해낼 수 있는 파생 필드 역시 굳이 비교할 필요는 없지만, 파생 필드를 비교하는 쪽이 더 빠를 때도 있다. (파생 필드가 객체 전체의 상태를 대표하는 상황)

`equals를 다 구현했다면 세 가지만 자문해보자. 대칭적인가? 추이성이 있는가? 일관적인가?`

```java
public final class PhoneNumber {
  
  private final short areaCode, prefix, lineNum;

  public PhoneNumber(int areaCode, int prefix, int lineNum) {
    this.areaCode = rangeCheck(areaCode, 999, "지역코드");
    this.prefix = rangeCheck(prefix, 999, "프리픽스");
    this.lineNum = rangeCheck(lineNum, 999, "가입자 번호");

    private static short rangeCheck(int val, int max, String arg) {
      if (val < 0 || val > max) {
        throw new IllegalArgumentException(arg + ": " + val);
      }
      return (short) val;
    }

    @Override
    public boolean eqauls(Object o) {
      if (o == this) {
        return true;
      }

      if (!(o instanceof PhoneNumber)) {
        return false;
      }

      PhoneNumber pn = (PhoneNumber) o;
      return pn.lineNum == lineNum 
        && pn.prefix == prefix 
        && pn.areaCode == areaCode;
    }
  }
}
```

- equals를 재정의할 땐 hashCode도 반드시 재정의하자
- 너무 복잡하게 해결하려 들지 말자. 필드들의 동치성만 검사해도 equals 규약을 어렵지 않게 지킬 수 있다.
- Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자.

사람이 직접 작성하는 것보다는 IDE에 맡기는 편이 낫다. (실수하지 않는...)
