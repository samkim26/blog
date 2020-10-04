---
title: class
date: 2020-10-03 15:10:79
category: javascript
draft: false
---

## CLASS

class는 어떤 공통된 속성이나 기능을 정의한 추상적인 개념이며, 이 class에 속한 객체를 instance라고 한다.

prototype 프로퍼티 내부에 할당되지 않고 생성자 함수 객체에 직접 할당되어 있는 프로퍼티들을 static methods, static properties 라고 칭한다.

보통 해당 클래스의 인스트들의 개별적인 동작이 아니라 공동체적인 판단을 필요로 하는 경우에 활요한다.

prototype 내부의 메소드들은 prototype methods라고 하는데, 앞에 prototype을 생략하고 말하기도 한다.

prototype 프로퍼티는 인스턴스와 __proto__로 연결되어 있고, __proto__는 생략가능하기 때문에 인스턴스에서 다이렉트로 prototype methods로 접근 가능하다. 하지만, 생성자 함수 내부에 있는 메소드나 프로퍼티는 다이렉트로 접근할 방법이 없다.

```javascript
function Person(name, age) {
    this._name = name;
    this._age = age;
}
// static method
Person.getInformations = function(instance) {
    return {
        name: instance._name,
        age: instance._age
    };
}
// (prototype) method
Person.prototype.getName = function() {
    return this._name;
}
// (prototype) method
Person.prototype.getAge = function() {
    return this._age;
}
```

```javascript
var gomu = new Person('고무', 30);

console.log(gomu.getName()); // ok
console.log(gomu.getAge()); // ok

console.log(gomu.getInformations(gomu)); // error

console.log(Person.getInformations(gomu)); // ok - 생성자 함수 직접 접근
```


## CLASS INHERITANCE

```javascript
function Person(name, age) {
    this.name = name || '이름없음';
    this.age = age || '나이모름';
}
Person.prototype.getName = function() {
    return this.name;
}
Person.prototype.getAge = function() {
    return this.age;
}

function Employee(name, age, position) {
    this.name = name || '이름없음';
    this.age = age || '나이모름';
    this.position = position || '직책모름';
}
Employee.prototype.getName = function() {
    return this.name;
}
Employee.prototype.getAge = function() {
    return this.age;
}
Employee.prototype.getPosition = function() {
    return this.position;
}
```
위 코드에서 getName(), getAge() 메소드는 겹친다.

겹치는 메소드는 상위 Person 에만 두고 Employee 에는 겹치지 않는 메소드를 두게 만든다.

```javascript
Employee.prototype = new Person();
Employee.prototype.constructor = Employee; // 원래 있던 prototype 객체와 같은 기능을 수행하도록 한다.
Employee.prototype.getPosition = function() { // prototype을 덮어씌운 다음에 정의한다.
    return this.position;
}
```

아래 코드와 같이 prototype chaining상의 불필요한 프로퍼티를 제거한다.

```javascript
function Bridge() {}
Bridge.prototype = Person.prototype;
Employee.prototype = new Bridge();
Employee.prototype.constructor = Employee;

Employee.prototype.getPosition = function() {
    return this.position;
}
```

최종본 - extendClass, superClass
```javascript
var extendClass = (function() {
    function Bridge() {}
    return function(Parent, Child) {
        Bridge.prototype = Parent.prototype;
        Child.prototype = new Bridge();
        Child.prototype.constructor = Child;
        Child.prototype.superClass = Parent;
    }
})();
function Person(name, age) {
    this.name = name || '이름없음';
    this.age = age || '나이모름';
}
Person.prototype.getName = function() {
    return this.name;
}
Person.prototype.getAge = function() {
    return this.age;
}
function Employee(name, age, position) {
    this.superClass(name, age);
    this.position = position || '직책모름';
}
extendClass(Person, Employee);
Employee.prototype.getPosition = function() {
    return this.position;
}
```

## 출처
> 인프런 Javascript 핵심 개념 알아보기 - JS Flow