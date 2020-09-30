---
title: prototype
date: 2020-09-30 13:09:94
category: javascript
draft: false
---

## prototype, constructor, \_\_proto__

Constructor 함수가 있을 때 new 연산자를 써서 instance를 만들면 생성자 함수의 prototype이라는 프로퍼티가 instance의 __proto__라는 프로퍼티에 전달이 된다.

생성자 함수의 prototype과 instance의 __proto__프로퍼티는 같은 객체를 참조한다.
__proto__는 내부 프로퍼티에 접근할 때 이 단어를 생략할 수 있다.

prototype 프로퍼티는 객체다.

생성자 타입의 프로토타입에 접근 가능한 방식 네 가지
- [CONSTRUCTOR].prototype
- [instance].\_\_proto__
- [instance]
- Object.getPrototypeOf([instance])

생성자 함수에 접근할 수 있는 다섯 가지
- [CONSTRUCTOR]
- [CONSTRUCTOR].prototype.consturctor
- (Object.getPrototypeOf([instace])).constructor
- [instance].\_\_proto__.constoructor


## 출처
> 인프런 Javascript 핵심 개념 알아보기 - JS Flow