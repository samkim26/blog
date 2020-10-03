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


## 메소드 상속 및 동작 원리

```javascript
function Person(n, a) {
    this.name = n;
    this.age = a;
}

var gomu = new Person('고무곰', 30);
var iu = new Person('아이유', 25);

gomu.setOlder = function() {
    this.age += 1;
}
gomu.getAge = function() {
    return this.age;
}
iu.setOlder = function() {
    this.age += 1;
}
iu.getAge = function() {
    return this.age;
}
```
위의 코드에서 반복을 줄여보자

```javascript
// 프로토타입으로 메소드를 이동시킴
function Person(n, a) {
    this.name = n;
    this.age = a;
}
Person.prototype.setOlder = function() {
    this.age += 1;
}
Person.prototype.getAge = function() {
    return this.age;
}

var gomu = new Person('고무곰', 30);
var iu = new Person('아이유', 25);
```

```javascript
gomu.__proto__.setOlder();
gomu.__proto__.getAge(); // NaN
```
NaN (Not a Number) 가 나오게 된다. 메소드들의 this의 gomu가 아니라 gomu.proto를 가리키고 있기 때문이다.
그런데 __proto__는 생략이 가능하기 때문에 마치 자신의 것처럼 메소드를 호출할 수 있게 해준다. 

```javascript
gomu.setOlder();
gomu.getAge(); // 31
```
이 경우에 this는 gomu를 가리킨다. 그래서 정상적으로 출력된다.

그런데 생성자 함수의 프로토타입에 age 프로퍼티가 있다면 결과는 달라진다.
```javascript
Person.prototype.age = 100;

gomu.__proto__.setOlder();
gomu.__proto__.getAge(); // 101

gomu.setOlder();
gomu.getAge(); // 31
```
메소드 호출 시 this가 어떻게 바인딩 되는지 상기!


## PROTOTYPE CHAINING

Array.prototype 은 객체이다.
근데 prototype이 객체라는 말은 Object 생성자 함수의 new 연산자로 생성된 인스턴스라는 말이 되고, 따라서 Object.prototype을 상속받게 된다.

prototype은 모두 객체이므로 모든 데이터타입은 protytpe chaining을 따른다.

각 생성자 함수 prototype에는 각 데이터타입에만 해당하는 메소드들이 정의되어있다.

모든 데이터타입에 대해서 생성자 함수의 prototype의 __로 연결된 Object.prototype에는 자바스크립트 전체를 통괄하는 공통된 메소스들이 정의되어있다.
이 메소드들은 모든 데이터타입이 prototype chaining을 통해서 접근할 수 있다.

```javascript
var arr = [1, 2, 3];

arr.toString = function() {
    return this.join('_');
}

console.log(arr.toString()); // 1_2_3

console.log(arr.__proto__.toString.call(arr)); // 1,2,3

console.log(arr.__proto__.__proto__.toString.call(arr)); // [object Array]

```

배열인 데이터는 모두 동일하게 출력하도록 한다면
```javascript
Array.prototype.toString = function() {
    return '[' + this.join(', ') + ']';
}


console.log(arr.toString()); // [1, 2, 3]

console.log(arr.__proto__.toString.call(arr)); // [1, 2, 3]

console.log(arr.__proto__.__proto__.toString.call(arr)); // [object Array]
```
배열에서 직접 호출한 것과 proto를 통해 호출한 것이 결과가 같다.

## 출처
> 인프런 Javascript 핵심 개념 알아보기 - JS Flow