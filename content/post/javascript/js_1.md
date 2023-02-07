---
title: "[javascript] 모던 자바스크립트 Deep Dive 10장"
date: 2023-02-07T14:46:04+09:00
draft: false
tags: ["Javascript"]
categories: ["(book)모던 자바스크립트 Deep Dive"]
showToc: true
UseHugoToc: true
comments: true
---

# 10장 객체 리터럴

## 1) 객체란?

- 자바스크립트는 **객체(object) 기반의 프로그래밍 언어**다. 원시값을 제외한 나머지 값(함수, 배열, 정규 표현식 등)은 모두 객체다.
- 객체 타입은 다양한 타입의 값을 하나의 단위로 구성한 복합적인 자료구조다.
- 객체 타입의 값은 **mutable value**다. (변할 수 있는 값)
- 객체는 0개 이상의 프로퍼티로 구성된 집합이며, **프로퍼티는 key-value**로 구성된다.
- 객체는 프로퍼티와 메서드로 구성된 집합체다.
  - 프로퍼티 : 객체의 상태를 나타내는 값
  - 메서드 : 프로퍼티를 참조하고 조작할 수 있는 동작
- 객체지향프로그래밍 : 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임

## 2) 객체 리터럴에 의한 객체 생성

- 자바스크립트는 프로토타입 기반 객체지향 언어로서 다양한 객체 생성 방법을 지원함.
  - 객체 리터럴 (가장 일반적인 방법)
  - Object 생성자 함수
  - 생성자 함수
  - Object.create 메서드
  - 클래스(ES6)
- code

  ```jsx
  //객체 리터럴 방식으로 객체 생성
  const user = {
    name: "slow",
  };

  console.log(typeof user); //object
  ```

## 3) 프로퍼티

- 객체는 **프로퍼티의 집합**이다. 프로퍼티는 키-값으로 구성된다.
  - property key: 빈 문자열을 포함하는 모든 문자열 또는 심벌 값. 식별자 역할을 함.
  - property value: 자바스크립트에서 사용할 수 있는 모든 값
- 식별자 네이밍 규칙을 준수하는 프로퍼티 키를 사용한다.

## 4) 메서드

- 메서드는 객체에 묶여있는 함수다. **프로퍼티 값이 함수일 경우** 일반 함수와 구분하기 위해 **메서드 (method)**라고 부른다.
- code

  ```jsx
  const user = {
    name: "slow",

    greeting: function () {
      //메서드
      return `안녕하세요. ${this.name}님.`;
    },
  };

  console.log(user.greeting()); //안녕하세요. slow님.
  ```

## 5) 프로퍼티

### 프로퍼티 접근과 값 갱신

- 접근 방법
  - 마침표 표기법(dot notation) → 마침표(.) 사용
  - 대괄호 표기법(bracket notation) → 대괄호([]) 사용
    - 대괄호 프로퍼티 접근 연산자 내에 지정하는 프로퍼티 키는 따옴표로 감싼 문자열이어야 함.
- 이미 존재하는 프로퍼티에 값을 할당하면 프로퍼티 값이 갱신됨.
- code
  ```jsx
  const user = {
    name: "slow",
  };
  //마침표 표기법
  console.log(user.name); //slow
  //대괄호 표기법
  console.log(user["name"]); //slow
  console.log(user[name]); //ReferenceError
  ```

### 프로퍼티 동적 생성과 삭제

- code

```jsx
//객체 리터럴 방식으로 객체 생성
const user = {
  name: "slow",
};
//ES5: 프로퍼티 키 동적 생성
user.age = 20;
//delete 연산자 활용해 age 프로퍼티 삭제
delete user.age;
```

## 6) [ES6] 객체리터럴 확장 기능

### 프로퍼티 축약 표현

- 프로퍼티 값으로 변수를 사용하는 경우 변수 이름과 프로퍼티 키가 동일한 이름일 때 프로퍼티를 생략할 수 있다.
- code
  ```jsx
  let nickname = "slow";
  let age = 20;
  //프로퍼티 축약 표현
  const user = { nickname, age };
  ```

### 계산된 프로퍼티 이름(computed property name)

- 문자열 또는 문자열로 타입 변환할 수 있는 값으로 평가되는 표현식을 사용해 프로퍼티 키를 동적으로 생성할 수 있음.
- code

  ```jsx
  const prefix = "prop";
  let i = 0;

  const obj = {
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
  };

  console.log(obj); //{prop-1: 1, prop-2: 2, prop-3: 3}
  ```

### 메서드 축약 표현

- 메서드를 정의할 때 function 키워드를 생략할 수 있음.
- code

```jsx
const user = {
  name: "slow",

  //메서드 축약 표현
  greeting() {
    return `안녕하세요. ${this.name}님.`;
  },
};

console.log(user.greeting()); //안녕하세요. slow님.
```

## 참고 자료

- [[book] 모던 자바스크립트 Deep Dive, 이웅모](https://product.kyobobook.co.kr/detail/S000001766445)
