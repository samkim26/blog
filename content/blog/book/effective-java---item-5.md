---
title: Effective Java - Item 5
date: 2022-04-05 15:04:23
category: book
draft: false
---

## 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

많은 클래스가 하나 이상의 자원에 의존한다.

아래 두 가지 경우는 잘못 사용의 예이며, 유연하지 않고 테스트하기 어렵다.

- 정적 유틸리티 클래스로 구현하는 경우
```java
public class SpellChecker {
    
    private static final Lexicon dictionary = ...;

    private SpellChecker() {} // 객체 생성 방지

    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String type) { ... }
}
```

- 싱글턴으로 구현하는 경우
```java
public class SpellChecker {

    private final Lexicon dictionary = ...;

    private SpellChecker(...) {}
    public static SpellChecker INSTANCE = new SpellChecker(...);

    public boolean isValid(String word) { ... }
    public List<String> suggestions(String type) { ... }
}
```

위의 두 방식 모두 사전을 단 하나만 사용한다고 가정한다는 점에서 훌륭해보이지 않다.

SpellChecker가 여러 사전을 사용할 수 있도록 만들어보자.
- 필드에서 final 한정자를 제거하고 다른 사전으로 교체하는 메서드를 추가할 수 있다.
  - 이 방식은 어색하고 오류를 내기 쉬우며 멀티스레드 환경에서는 쓸 수 없다.
- **사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.**
- 클래스(SpellChecker)가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원(dictionary)을 사용해야 한다. -> **인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식** - 의존 객체 주입의 한 형태
- 의존 객체 주입은 유연성과 테스트 용이성을 높여준다.

```java
public class SpellChecker {
    
    private final Lexicon dictionary;

    // 의존 객체 주입
    public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
    }
    
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String type) { ... }
}
```

의존 객체 주입 패턴은
- 아주 단순하다.
- 예에서는 dictionary 라는 딱 하나의 자원만 사용하지만, 자원이 몇 개든 의존 관계가 어떻든 상관없이 잘 작동한다.
- 불변을 보장하여 (같은 자원을 사용하려는)여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있기도 하다.
- 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용할 수 있다.

이 패턴의 쓸만한 변형으로, 생성자에 자원 팩터리를 념겨주는 방식이 있다.
> 팩터리란 호출할 때마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체를 말한다.

- 팩터리 메서드 패턴(Factory Method pattern)을 구현한 것이다.
- 자바 8에서 Supplier\<T> 인터페이스가 팩터리를 표현한 완벽한 예
- Supplier\<T>를 입력받는 메서드는 일반적으로 한정적 와일드카드 타입(bounded wildcard type)을 사용해 팩터리의 타입 매개변수를 제한해야 한다. 이 방식을 사용해 클라이언트는 자신이 명시한 타입의 하위타입이라면 무엇이든지 생성할 수 있는 팩터리를 넘길 수 있다.
    ```java
    Mosaic create(Supplier<? extends Tile> tileFactory) { ... }
    ```

의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 수 천 개나 되는 큰 프로젝트에서는 코드를 어지럽게 만들기도 한다.  
-> 의존 객체 프레임워크를 사용하면 해소할 수 있다. (Spring!🌼)

### 정리
- 클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 직접 만들게 해서도 안 된다.
- 필요한 자원을 (혹은 그 자원을 만들어 주는 팩터리를) 생성자에 (혹은 정적 팩터리나 빌더에) 넘겨주자.
- 의존 객체 주입이라 하는 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.