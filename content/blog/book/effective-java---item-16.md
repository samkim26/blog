---
title: Effective Java - Item 16
date: 2022-05-25 11:11:02
category: book
draft: false
---

## public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

```java
class Point {
  public double x;
  public double y;
}
```

이런 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못한다.
-> 필드들을 모두 private으로 바꾸고 public 접근자(getter)를 추가한다.

```java
class Point {
  private double x;
  private double y;

  public Point(double x, double y) {
    this.x = x;
    this.y = y;
  }

  public double getX() { return x; }
  public double getY() { return y; }

  public void setX(double x) { this.x = x; }
  public void setY(double y) { this.y = y; }
}

```

public 클래스에서라면 이 방식이 확실히 맞다.

**패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공**함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있다.

**package-private 클래스 혹은 private 중접 클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다.** 그 클래스가 표현하려는 추상 개념만 올바르게 표현해주면 된다. (클라이언트도 어차피 이 클래스를 포함하는 패키지 안에서만 동작하는 코드일 뿐)

public 클래스의 필드가 불변이라면 직접 노출할 떄의 단점이 조금은 줄어 들지만, 여전히 결코 좋은 생각이 아니다. API를 변경하지 않고는 표현 방식을 바꿀 수 없고, 필드를 읽을 때 부수 작업을 수행할 수 없다는 단점은 여전하다. 단, 불변식은 보장할 수 있게 된다.