---
title: this
date: 2020-09-27 14:09:89
category: javascript
draft: false
---

## THIS

각각 this가 가리키는 것이 무엇인지 알아봅시다!

1. 전역공간에서 -> window / global
브라우저 콘솔에서 : window 객체 / node.js에서 : global 객체

2. 함수 내부에서 -> window / global
함수 내부에서 기본적으로는 전역객체를 가리키지만 바뀔 수 있다.

3. 메소드 호출시 -> 메소드 호출 주체(메소드명 앞)
함수는 (전역객체의)메소드다!(라고 생각하자)
메소드 내의 내부 함수가 있을 때에 this 는 까다로워질 수 있다.

4. callback에서 -> 기본적으로는 함수내부에서와 동일

> call, apply, bind 메소드에 대하여  
call, apply 는 즉시호출, bind 는 새로운 함수 생성(currying)

- 기본적으로는 함수의 this와 같다.
- 제어권을 가진 함수가 callback의 this를 명시한 경우 그에 따른다.

5. 생성자함수에서 -> 인스턴스
