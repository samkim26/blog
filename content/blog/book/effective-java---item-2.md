---
title: Effective Java - Item 2
date: 2021-11-10 15:11:60
category: book
draft: false
---

## 생성자에 매개변수가 많다면 빌더를 고려하라

정적 팩터리와 생성자에는 똑같은 제약이 하나 있다. 선택적 매개변수가 많을 때 적절히 대응하기 어렵다는 점이다.

### 대안 1 : 점층적 생성자 패턴(telescoping constructor pattern)

선택 매개변수를 전부다 받는 생성자까지 늘려가는 방식

```
필수 매개변수만 받는 생성자,
필수 매개변수와 선택 매개변수 1개를 받는 생성자,
...
```

**점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어렵다.**

- 코드를 읽을 때 각 값의 의미가 무엇인지 헷갈릴 것이다.
- 매개변수가 몇 개인지도 주의해서 세어 보아야 할 것이다.
- 타입이 같은 매개변수가 연달아 늘어서 있으면 찾기 어려운 버그로 이어질 수 있다.
- 클라이언트가 실수로 매개변수의 순서를 바꿔 건네줘도 컴파일러는 알아채지 못하고, 결국 런타임에 엉뚱한 동작을 하게 된다.


### 대안 2 : 자바빈즈 패턴(JavaBeans pattern)

매개변수가 없는 생성자로 객체를 만든 후, setter 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식

- 객체 하나를 만들려면 메서드를 여러 개 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 된다.
  - 점층적 생성자 패턴에서는 매개변수들이 유효한지를 생성자에서만 확인하면 일관성을 유지할 수 있었는데, 그 장치가 완전히 사라진 것이다.
- 일관성이 깨진 객체가 만들어지려면, 버그를 심은 코드와 그 버그 때문에 런타임에 문제를 겪는 코드가 물리적으로 멀리 떨어져 있을 것이므로 디버깅도 만만치 않다.
- 클래스를 불변으로 만들 수 없다.
- 스레드 안정성을 얻으려면 프로그래머가 추가 작업을 해줘야만 한다.


### ✅ 대안 3 : 빌더 패턴(Builder pattern)

- 점층적 생성자 패턴의 안정성 + 자바빈즈 패턴의 가독성
  
- 클라이언트는 필요한 객체를 직접 만드는 대신, 필수 매개변수만으로 생성자(혹은 정적 팩터리)를 호출해 빌더 객체를 얻는다.
- 빌더 객체가 제공하는 일종의 setter 메서드들로 원하는 선택 매개변수들을 설정한다.
- 매개변수가 없는 build 메서드를 호출해 드디어 우리에게 필요한(보통은 불변인) 객체를 얻는다.

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }
        public Builder fat(int val) {
            fat = val;
            return this;
        }
        public Builder sodium(int val) {
            sodium = val;
            return this;
        }
        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```

- 빌더의 setter 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출할 수 있다.
- 이런 방식을 `플루언트 API(fluent API)` 혹은 `메서드 연쇄(method chaining)`라 한다.
- 빌더 패턴은 (파이썬과 스칼라에 있는) 명명된 선택적 매개변수(named optional parameters)를 흉내 낸 것이다.
- 잘못된 매개변수를 최대한 일찍 발견하려면 빌더의 생성자와 메서드에 입력 매개변수를 검사하고, build 메서드가 호출하는 생성자에서 여러 매개변수에 걸친 불변식(invariant)을 검사하자
    > 불변식(invariant)은 프로그램이 실행되는 동안, 혹은 정해진 기간 동안 반드시 만족해야 하는 조건을 말한다. 다시 말해 변경을 허용할 수 있으나 주어진 조건 내에서만 허용한다는 뜻이다.
- 빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.
- 빌더를 이용하면 가변인술 매개변수를 여러 개 사용할 수 있다.

**단점**
- 객체를 만들려면, 그에 앞서 빌더부터 만들어야 한다.
- 빌더 생성 비용이 크진 않지만 성능에 민감한 상황에서는 문제가 될 수 있다.
- 점층적 생성자 패턴보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 한다.
  - 하지만, API는 시간이 지날수록 매개변수가 많아지는 경향이 있음을 명심하자.

생성자나 정적 팩터리 방식으로 시작했다가 나중에 매개변수가 많아지면 빌더 패턴으로 전환할 수도 있다. 애초에 빌더로 시작하는 편이 나을 때가 많다.