---
title: Effective Java - Item 14
date: 2022-05-14 10:03:10
category: book
draft: false
---

## Comparable을 구현할지 고려하라

Comparable 인터페이스의 유일무이한 메서드인 `compareTo`

compareTo는 Object의 메서드가 아니다.

compareTo가 Object의 equals와 다른 점
- 단순 동치성 비교에 더해 순서까지 비교할 수 있음
- 제네릭함

Comparable을 구현했다는 것은 그 클래스의 인스턴스들에는 자연적인 순서가 있음을 뜻한다. 그래서 Comparable을 구현한 객체들의 배열은 손쉽게 정렬할 수 있다. 

```java
Arrays.sort(a);
```

자바 플랫폼 라이브러리의 모든 값 크래스와 열거 타입이 Comparable을 구현했다. 알파벳, 숫자, 연대 같이 순서가 명확한 값 클래스를 작성한다면 반드시 Comparable 인터페이스를 구현하자.

```java
public interface Comparable<T> {
    int compareTo(T t);
}
```

compareTo 메서드의 일반 규약은 equals의 규약과 비슷하다.
- Comparable을 구현한 모든 클래스는 모든 x, y에 대해 sgn(x.compareTo(y)) == -sgn(y.compareTo(x))여야 한다.
- Comparable을 구현한 클래스는 추이성을 보장해야 한다.
  - x.comapareTo(y) > 0 && y.compareTo(z) > 0 이면 x.compareTo(z) > 0 이다.
- Comparable을 구현한 클래스는 모든 z에 대해 x.compareTo(y) == 0이면 sgn(x.compareTo(z)) == sgn(y.compareTo(z))다.
- 필수는 아니지만 꼭 지키는게 좋다. : (x.compareTo(y) == 0) == (x.equals(y))여야 한다.

모든 객체에 대해 전역 동치관계를 부여하는 equals 메서드와 달리, compareTo는 타입이 다른 객체를 신경 쓰지 않아도 된다. 타입이 다른 객체가 주어지면 간단히 ClassCastException을 던져도 되며, 대부븐 그렇게 한다.

Comparable을 구현한 클래스를 확장해 값 컴포넌트를 추가하고 싶다면, 확장하는 대신 독립된 클래스를 만들고, 이 클래스에 원래 클래스의 인스턴스를 가리키는 필드를 두자. 그런 다음 내부 인스턴스를 반환하는 '뷰' 메서드를 제공하면 된다.
> 상속 보다 컴포지션을 활용하자는 것 같다.

마지막 규약은 compareTo 메서드로 수행한 동치성 테스트의 결과가 equals와 같아야 한다는 것이다. 이를 잘 지키면 compareTo로 줄지은 순서와 equals의 결과가 일관되게 된다.

Comparable은 타입을 인수로 받는 제네릭 인터페이스이므로 compareTo 메서드의 인수 타입은 컴파일타임에 정해진다. 입력 인수의 타입을 확인하거나 형변환할 필요가 없다는 뜻이다.

compareTo 메서드는 각 필드가 동치인지를 비교하는 게 아니라 그 순서를 비교한다. 객체 참조 필드를 비교하려면 compareTo 메서드를 재귀적으로 호출한다. Comparable을 구현하지 않은 필드나 표준이 아닌 순서로 비교해야 한다면 비교자(Comparator)를 대신 사용한다.

compareTo 메서드에서 관계 연산자 <와 >를 사용하는 이전 방식은 거추장스럽고 오류를 유발하니, 이제는 추천하지 않는다.
> 정수, 실수 모두 정적 메서드 사용

클래스에 핵심 필드가 여러 개라면 어느 것을 먼저 비교하느냐가 중요해진다. 가장 핵심적인 필드부터 비교해나가자.

자바 8에서는 Comparator 인터페이스가 일련의 비교자 생성 메서드(comparator construction method)와 팀을 꾸려 메서드 연쇄 방식으로 비교자를 생성할 수 있게 되었다. 이 방식의 간결함에 매혹되지만, 약간의 성능 저하가 뒤따른다. (정적 임포트 기능을 이용하면 코드가 훨씬 깔끔해진다.)

- 정적 compare 메서드를 활용한 비교자
```java
static Comparator<Object> hashCodeOrder = new Comparator<>() {
    public int compare(Object o1, Object o2) {
        return Integer.compare(o1.hashCode(), o2.hashCode());
    }
};
```
- 비교자 생성 메서드를 활용한 비교자
 ```java
static Comparator<Object> hashCodeOrder = Comparator.comparingInt(o -> o.hashCode());
```

### 핵심 정리

- 순서를 고려해야 하는 값 클래스를 작성한다면 꼭 Comparable 인터페이스를 구현하여, 그 인스턴스들을 쉽게 정렬하고, 검색하고, 비교 기능을 제공하는 컬렉션과 어우러지도록 해야 한다.
- compareTo 메서드에서 필드의 값을 비교할 때 <와 > 연산자는 쓰지 말아야 한다.
- 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator 인터페이스가 제공하는 비교자 생성 메서드를 사용하자.

