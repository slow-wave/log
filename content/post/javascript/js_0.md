---
title: "[javascript] ES6+ 핵심 문법 정리"
date: 2023-01-31T16:31:43+09:00
draft: false
tags: ["Javascript"]
categories: ["Javascript"]
showToc: true
UseHugoToc: true
comments: true
---

## 0) Intro

javascript 문법 공부를 위해 Inflearn에 있는 강의를 듣고 정리한 글입니다. 두 강의 모두 자바스크립트 문법을 공부하는데 많은 도움이 되어 추천하고 싶습니다. 코드 예제의 경우 공부를 위해서 변형한 부분이 있습니다.

- [이룸코딩님의 **자바스크립트 ES6+ 기초 핵심 문법**](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-es6-%EA%B8%B0%EC%B4%88%ED%95%B5%EC%8B%AC/community?type=question)
- [코딩앙마님의 자바스크립트 중급 강좌](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%A4%91%EA%B8%89/dashboard)

## 1) 블록 범위 생성자

### 스코프

- 스코프는 변수에 접근 가능한 범위임.
- 종류
  - **함수 레벨 스코프**: 함수 블록 내에서 선언된 변수는 함수 내에서만 접근 가능
    - 기존의 변수 선언 키워드인 var는 함수 스코프
  - **블록 레벨 스코프**:함수를 포함한 모든 코드 블록(함수, if 문, for문, while문, try/catch 문등) 내에서 선언된 변수는 코드 블록 내에서만 접근 가능
    - let, const는 블록 스코프
- var의 문제점
  - 함수를 제외한 다른 코드 블록에서는 개별적인 스코프를 가질 수 없다는 문제점 있음.
  - 재선언 허용
    → ES6에서 let, const 추가

### let, const

- 변수(let)
  - 변할 수 있는 값을 저장하는 공간
  - 생성 과정
    - (1) 선언 단계
    - (2) 초기화 단계(undefined를 할당 해주는 단계)
    - (3) 할당 단계
- 상수(const)
  - 변할 수 없는 값을 저장하는 공간
  - 선언할 때 항상 초기화해야함.
  - 생성 과정
    - (1) 선언 + 초기화 + 할당
- 공통 규칙
  - 변수나 상수 이름의 첫번째 글자는 영어, $, \_만 가능
  - 일반적으로는 소문자로 선언, 상수를 선얼할 때는 대문자 사용
  - let, const와 같은 자바스크립트 예약어는 사용 불가
  - 이름에 공백은 허용되지 않음.
    - Camel Case 방식 (whoAmI)
    - Snake Case 방식(who_am_i)
- code

  ```jsx
  let hi;
  hi = "hello";

  const hello = "hello";
  ```

## 2) 데이터 타입

### 원시 데이터 타입

- 객체가 아니면서 메서드도 가지지 않는 데이터
- 종류
  - string, number, bigint, boolean, undefined, null, symbol
- code

  ```jsx
  //number
  const number = 123;

  //bigint
  const bigInt = 123n;

  //string
  const str = "hi";

  //boolean
  const isTrue = true;

  //symbol
  const a = Symbol();
  const b = Symbol();
  console.log(a === b); //false

  const id = Symbol("id");
  ```

### 참조 데이터 타입

- Array
  - code
    ```jsx
    //Array
    const arr = [1, 2, 3];
    console.log(arr);
    ```
- Object

  - 객체는 변수나 값 외에도 함수를 property로 가질 수 있음.
  - code

    ```jsx
    //Object
    const obj = {
      name: "slow",
      job: "개발자",
    };
    //(1)Object에 접근하는 방법
    //점 표기법
    console.log(obj.name);
    //대괄호 표기법
    console.log(obj["job"]);

    //(2)Object.assign():객체 복제
    const newUser = Object.assign({}, obj);

    //(3)key(s) & values 추출

    //Object.keys() (전역객체 Object안에 있는 Keys라는 정적 메소드)
    console.log(Object.keys(obj)); //['name', 'job']

    //Object.values(): 값 배열 반환
    console.log(Object.values(obj)); // ['slow', '개발자']

    //Object.entries(): 키/값 배열 반환
    console.log(Object.entries(obj));

    //Object.fromEntries(): 키/값 배열을 객체로
    console.log(Object.fromEntries(obj));

    //(4)배열과 객체는 외부 중첩 가능
    const arr1 = [1, 2, 3, [4, 5]];
    console.log(arr1); //[1, 2, 3, Array(2)]

    //(5)객체의 축약표현
    const name = "coffee";
    const price = "5000";
    const budget = "10000";
    const product = {
      name,
      price,
      budget,
      change() {
        console.log(`잔돈은 ${budget - price} 원 입니다.`);
      },
    };

    console.log(product);
    product.change(); //잔돈은 5000 원 입니다.
    ```

### 원시 타입 vs. 참조 타입

- 원시 타입의 경우 메모리에 변수를 위한 공간이 할당됨. (고정된 크기를 한번에 할당받음.)
  → 원시 타입 값이 저장
- 참조 타입의 경우 원시 타입 데이터의 집합을 저장
  → Heap에 값이 저장
  → 변수에는 메모리 값을 저장하는 주소값만 저장. 변수를 사용할 때 주소값을 참조해서 사용함.

## 3) 연산자

- 산술 연산자
  - +, -, \*, /, \*\*, %
- 증감 연산자
  - ++, --
- 비교 연산자

  - <, >, ===, !==
  - == vs. ===

    - code

      ```jsx
      //비교 연산자
      const a = 123;
      const b = "123";

      console.log(a == b); //true (추상 비교)
      console.log(a === b); //fasle (엄격 비교)
      ```

- 논리 연산자

  - code

    ```jsx
    //논리 연산자
    const a = true;
    const b = false;

    console.log(a && b); //false
    console.log(a || b); //true
    console.log(!a); //false
    ```

- 삼항 연산자
  - code
    ```jsx
    //삼항연산자
    //condition ? true : false
    const toc = true;
    console.log(toc ? "open" : "close");
    ```
- nullish 연산자

  - 여러 개의 피연산자 중 값이 확정되어 있는 변수를 찾음.
  - code

    ```jsx
    //nullish
    const a = undefined;
    const b = null;
    const c = "hi";

    console.log(a ?? b ?? c); //hi
    ```

- 비트 연산자
  - &, |, ~, ^, <<, >>
- 대입 연산자
  - 복합 대입 연산자
  - code
    ```jsx
    let number = 10;
    number += 2;
    ```
- 전개 연산자(spread syntax)

  - 반복이 가능한 객체에 적용할 수 있는 문법의 한 종류다.
  - code

    ```jsx
    //전개 연산자
    const numbers = [1, 2, 3, 4, 5];
    const numbers2 = [6, 7, 8];

    console.log(...numbers); //1 2 3 4 5
    console.log(...numbers, ...numbers2); //1 2 3 4 5 6 7 8
    ```

## 4) 조건문

### if

- code

  ```jsx
  const budget = 4000;
  const price = 3000;
  //if 구문
  if (budget >= price) console.log("구매 가능");

  //if-else 구문
  if (budget >= price) {
    console.log("구매 가능");
  } else {
    console.log("구매 불가");
  }

  //if-else if-if 구문
  if (budget > price) {
    console.log(`잔돈은 ${budget - price}원`);
  } else if (budget === price) {
    console.log("잔돈은 0원");
  } else {
    console.log("구매 불가");
  }
  ```

### switch

- code

  ```jsx
  //switch
  const number = 10;

  switch (number % 2) {
    case 0:
      console.log("짝수");
      break;
    case 1:
      console.log("홀수");
      break;
  }
  ```

## 5) 반복문

### for / for of

- code

  ```jsx
  //for
  for (let i = 0; i <= 10; i++) {
    if (i % 2 === 0) continue;
    console.log(i);
  }

  //for of
  const arr = [1, 2, 3];
  for (const i of arr) {
    console.log(i);
  }
  ```

### while / do while

- code

```jsx
//while
let i = 0;

while (i < 10) {
  console.log(i++);
}

//do while
do {
  console.log(i++);
} while (i < 10);
```

## 6) 함수

- 인자와 매개변수
  - 인자 = 함수의 입력값=Arguments / 매개변수 = 함수의 입력변수 = Parameter
  - 자바스크립트에서는 값이 할당되지 않은 경우 undefined 할당
  - 필요한 인자 수보다 더 많은 수 입력 → 에러 발생X
  - 필요한 인자 수보다 더 많은 수가 입력 → 초과된 인수는 무시될 뿐 에러 발생X
- 함수 내부에서 선언된 변수를 지역 변수(local variable)라고 함.
  - 변수를 사용할 수 있는 범위(scope)를 block안으로 제한
- code

  ```jsx
  //(1)인자에 기본값 설정 X
  function sum(a, b) {
    console.log(a + b);
  }

  sum(10, 20); //30

  //(2)인자에 기본값 설정
  function sum(a, b = 10) {
    console.log(a + b);
  }

  sum(10); //20

  //(3)지정한 인자를 제외한 나머지를 묶어주는 방법
  function print(a, b, ...rest) {
    console.log(a);
    console.log(b);
    console.log(rest);
  }

  print(10, 20, 30, 40); //10,20,[30,40]
  ```

## 7) 함수의 표현식과 화살표 함수

- 화살표 함수는 ES6에서부터 지원하는 새로운 함수 문법임.
- code

```jsx
//(1)함수 표현식1
function greeting(user) => {
  console.log(`안녕하세요! ${user}님`);
};

greeting("slow");

//(2)함수 표현식2
const greeting1 = (user) => {
  console.log(`안녕하세요! ${user}님`);
};

greeting1("slow");
```

## 8) 숫자, 수학 method(Number, Math)

```jsx
// (1) toString() : 10진수 -> 2진수/16진수
let num = 10;
num.toString(); //"10"
num.toString(2); //"1010"
num.toString(16); //a

//(2) Math
let num1 = 5.1;
let num2 = 5.7;
Math.ceil(num1); //6
Math.floor(num1); //5
Math.round(num1); //5
Math.round(num2); //6

//(3) toFixed() -> 문자열로 반환함.
let userRate = 30.9876;
userRate.toFixed(2); //"30.99"

//(4) isNan()
let x = Number("x"); //NaN
console.log(x == NaN); //false
console.log(x === NaN); //false
console.log(NaN == NaN); //false
console.log(isNaN(x)); //true
console.log(isNaN(3)); //false

//(5) parseInt() -> 문자열을 숫자로 바꿔줌.
let margin = "10px";
parseInt(margin); //10
parseInt("11", 2); //3 (이진수로 변환)

//(6) parseFloat()
let padding = "18.5%";
parseFloat(padding); //18.5

//(7) Math.random() -> 0~1사이 무작위 숫자 생성
//1~100사이 임의의 수 뽑기
const numbers = Math.floor(Math.random() * 100) + 1;
console.log(numbers);

//(8) Math.max() / Math.min()
Math.max(1, 2, 3, 4); //4
Math.min(1, 2, 3, 4); //1

//(9)Math.abs()
Math.abs(-10); //10

//(10) Math.pow(n,m) -> n ** m
Math.pow(2, 10); //1024

//(11) Math.sqrt() -> 제곱근
Math.sqrt(100); //10
```

## 9) 문자열 메소드

- 대소문자
  - toUpperCase() / toLowerCase()
- 공백 제거
  - trim() / trimStart() / trimEnd()
- padding
  - padStart(n, str)
  - padEnd(n,str)
  - n : 문자열 길이, str : 추가할 문자열
- repeat(n)
  - n : 반복횟수
- indexOf(str)
  - str: 찾을 문자열
  - 있으면 시작값 인덱스, 없으면 -1 반환
- includes(str)
  - str: 확인할 문자열
  - 있으면 true, 없으면 false 반환
- startsWith(str) / endsWith(str)
  - 있으면 true, 없으면 false 반환
- replace(prev, cur)
  - prev: 바꿀 문자열
  - cur : 변경 문자열
- substring(start, end)
  - start: 시작인덱스
  - end: 끝 인덱스
- split(str)
  - str : 구분 기준 문자열
- slice(start, end)
  - start: 시작인덱스
  - end: 끝 인덱스
- code

  ```jsx
  //(0)문자열 비교
  "a".codePointAt(0); //97
  String.fromCodePoint(97); //a

  //(1)repeat
  const str = "hi!";

  //새로운 문자열 반환 (원복 객체는 변하지 않음.)
  console.log(str.repeat(3)); //hi!hi!hi!
  console.log(str); //hi!

  //(2)indexOf
  const str1 = "안녕하세요. 저의 이름은 slow 입니다.";
  console.log(str1.indexOf("이름은")); //10

  const arr = [1, 2, 3, 4, 5, 1, 2, 3];
  arr.indexOf(3); //2
  arr.indexOf(3, 3); //7
  arr.lastIndexOf(3); //7

  //(3)replace
  const str2 = "안녕하세요. 저의 이름은 slow 입니다.";
  console.log(str2.replace("slow", "slowwave"));

  //(4)substring
  const str3 = "안녕하세요. 저의 이름은 slow 입니다.";
  console.log(str3.substring(0, 5)); //안녕하세요

  //(5)split
  const weekday = "월, 화, 수, 목, 금";
  console.log(weekday.split(",")); //['월', ' 화', ' 수', ' 목', ' 금']

  //(6)slice
  const hi = "안녕하세요.";
  console.log(hi.slice(0, 2)); //안녕

  //(7)slice 응용
  let list = ["01. 하나", "02. 둘", "03. 셋"];
  let newList = [];
  for (let i = 0; i < list.length; i++) {
    newList.push(list[i].slice(4));
  }
  console.log(newList);
  ```

## 9) 배열 메소드

- arr.push(), arr.pop()

  - code

    ```jsx
    //arr.push(), arr.pop()
    const arr = [1, 2, 3, 4, 5];
    arr.push(6);
    arr.push(7, 8);
    arr.pop();
    arr.unshift(0);

    console.log(arr); //[0, 1, 2, 3, 4, 5, 6, 7]
    ```

- arr.forEach()

  - 반환값 없음.
  - code

    ```jsx
    //foreach 1
    function print(number, index) {
      console.log(`${index} 위치의 요소 : ${number}`);
    }

    arr.forEach(print);

    //foreach 2
    arr.forEach((number, index) => {
      console.log(`${index} 위치의 요소 : ${number}`);
    });
    ```

- arr.map()

  - 함수를 받아 특정 기능을 시행하고 새로운 배열을 반환함.
  - code

    ```jsx
    //map1
    const arr = [1, 2, 3, 4, 5];
    const newArr = arr.map((number) => number + 1);
    console.log(newArr); //[2,3,4,5,6]

    //map2
    let userList = [
      { name: "a", age: 10 },
      { name: "b", age: 15 },
      { name: "c", age: 30 },
    ];

    let newUserList = userList.map((user, index) => {
      return Object.assign({}, user, {
        id: index + 1,
        isAdult: user.age > 19,
      });
    });
    console.log(newUserList);
    //output > [{name: 'a', age: 10, id: 1, isAdult: false},{name: 'b', age: 15, id: 2, isAdult: false},{name: 'c', age: 30, id: 3, isAdult: true}]
    ```

- arr.includes()/arr.find()/arr.findIndex()

  - code

    ```jsx
    //arr.includes()
    const arr = ["hello", "world"];
    console.log(arr.includes("hello")); //true

    //arr.find()
    const arr1 = [1, 2, 3, 4, 5];
    console.log(arr1.find((number) => number > 3)); //4 (첫번째 요소만 리턴, 없으면 undefined를 반환)

    //arr.findIndex()
    console.log(arr1.findIndex((number) => number > 3)); //3 (첫번째 요소의 인덱스 리턴)
    ```

- arr.slice()/arr.splice()

  - code

    ```jsx
    //arr.slice()
    const arr = [1, 2, 3, 4, 5];
    console.log(arr.slice(2, 4)); //[3,4]

    //arr.splice()
    arr.splice(4);
    console.log(arr); //[1,2]
    arr.splice(2, 1, 100);
    console.log(arr); //[1,2,100,4]
    ```

- arr.join()/arr.concat()

  - code

    ```jsx
    const hi = ["hi", "slow"];
    const greeting = ["nice to meet u"];
    //arr.join()
    console.log(hi.join("-")); //hi-slow

    //arr.concat()
    console.log(hi.concat(greeting)); //['hi', 'slow', 'nice to meet u']
    ```

- arr.filter()

  - 만족하는 모든 요소를 배열로 반환
  - code

    ```jsx
    //filter
    let userList = [
      { name: "a", age: 10 },
      { name: "b", age: 15 },
      { name: "c", age: 30 },
    ];

    const result = userList.filter((user) => {
      if (user.age < 19) return true;
      return false;
    });

    console.log(result); //[{name:'a', age:10},{name:'b', age:15}]
    ```

- arr.reduce(callback, [,initialValue])
  - 배열의 각 요소에 대해서 주어진 reducer 함수 실행 후 하나의 값을 반환
  - 필수 인자인 누산기(acc), 현재 값(cur)을 가짐.
  - 선택적으로 현재 인덱스(idx), 원본 배열(src) 가짐.
  - code
    ```jsx
    //reduce
    const arr = [1, 2, 3, 4, 5];
    const reducer = (acc, value) => acc + value;
    console.log(arr.reduce(reducer)); //15
    ```
- arr.reverse()
  - code
  ```jsx
  //arr.reverse() : 역순으로 재정렬
  let arr1 = [1, 2, 3, 4, 5];
  arr1.reverse();
  console.log(arr1); //[5,4,3,2,1]
  ```
- arr.sort()
  - 이 함수를 이용하면 유니코드 값 순서대로 정렬됨.
  - 정렬 수행시 Lodash 라이브러리 사용함.
  - code
    ```jsx
    let arr = [21, 15, 200, 100];
    //정렬 실패
    arr.sort();
    console.log(arr); //100, 15, 200, 21
    //정렬 성공
    arr.sort((a, b) => {
      return a - b;
    });
    console.log(arr); //15,21,100,200
    ```

## 10) 구조분해할당

- 배열 또는 객체의 구조를 분해해 값을 개별적 변수에 담는 표현식임.

### 배열 구조분해

- code

  ```jsx
  //배열 구조분해
  const arr = [1, 2, 3, 4, 5];

  const [one, two, three] = arr;
  console.log(one, two, three); //1 2 3
  ```

### 객체 구조분해

- code

  ```jsx
  //객체 구조분해1
  const obj = {
    x: 10,
    y: 20,
  };

  const { x, y } = obj;
  console.log(x, y);

  const { x: one, y: two } = obj;
  console.log(one, two);

  //객체 구조분해2
  const ex = {
    one: {
      two: {
        three: "hi",
      },
    },
  };

  const {
    one: {
      two: { three },
    },
  } = ex;
  console.log(three);
  ```

- 함수 구조분해
  - code
    ```jsx
    //함수 구조분해
    const obj = {
      x: 10,
      y: 20,
    };
    //ex1
    function sum(obj) {
      //점 표기법으로 접근
      return obj.x + obj.y;
    }
    console.log(sum(obj));
    //ex2
    function sum1({ x, y }) {
      return x + y;
    }
    console.log(sum(obj));
    ```
- 구조분해 할당 응용

  - code

    ```jsx
    //(1)구조분해 할당 - 변수 간 값 바꾸기
    let a = 123;
    let b = 456;

    [a, b] = [b, a];
    console.log(a, b); //456 123

    //(2)구조분해 할당 - 기본값 할당
    const [c = 10, d = 20] = [10];
    console.log(c, d); //10 20

    //(3)구조분해 할당 - 중간값 생략
    const arr = [1, 2, 3, 4, 5];
    const [one, , three] = arr;
    console.log(one, three); //1 3

    //(4)구조분해 할당
    const [, two, ...others] = arr;
    console.log(two, others); //2 [3,4,5]
    ```

## 11) 생성자 함수

```jsx
//생성자 함수
//생성자 함수 이름의 첫 글자는 대문자
function User(name, age) {
  this.name = name;
  this.age = age;
  this.message = function () {
    console.log(`이름은 ${this.name}이고 나이는 ${this.age}살 입니다.`);
  };
}

let user1 = new User("slow", 27);
let user2 = new User("mm", 20);
user2.message(); //이름은 mm이고 나이는 20살 입니다.
```

## 12) 계산된 프로퍼티(Computed property)

```jsx
//계산된 프로퍼티
let a = "age";
const user = {
  name: "Mike",
  [a]: 30, //변수 a에 할당된 값 들어감. -> computed property
};
```

## 13) DOM(Document Object Model)

- HTML 문서에 있는 어떤 요소를 조작 or 새로운 요소 추가 or 기존의 요소 삭제
- code (index.html)
  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>Document</title>
    </head>
    <body>
      <h2 id="title" class="title_class">Hello</h2>
      <button>click!</button>
      <button class="remove">이벤트 삭제</button>
      <script src="index.js"></script>
    </body>
  </html>
  ```
- 요소 선택

  - code

    ```jsx
    //요소 선택1
    //id로 찾기
    const title = document.getElementById("title");
    console.log(title);

    //class로 찾기 - class는 고유값이 아니기 때문에 배열로 반환됨.
    const title_class = document.getElementsByClassName("title_class");
    console.log(title_class);

    //tag 이름으로 찾기
    const title_tag = document.getElementsByTagName("h2");
    console.log(title_tag);
    ```

  - code

    ```jsx
    //요소 선택2
    //querySelector
    const title = document.querySelector("h2");
    console.log(title);
    const title_tag = document.querySelector("#title");
    console.log(title_tag);
    const title_class = document.querySelector(".title_class");
    console.log(title_class);

    //querySelectorAll
    const title_classes = document.querySelectorAll(".title_class");
    console.log(title_classes);
    ```

- 요소 변경
  - 선택한 요소들이 innerText를 사용할 수 있는 이유는 HTML element라는 객체가 갖고 있는 하나의 property이고, HTML 모든 요소들이 HTML element라는 객체를 상속받고 있기 때문이다.
  - [HTMLElement docs](https://developer.mozilla.org/ko/docs/Web/API/HTMLElement)
  - code
    ```jsx
    //요소 변경
    const title = document.querySelector("h2");
    title.innerText = "안녕하세요!";
    title.style.color = "blue";
    ```
- 요소 생성

  - code

    ```jsx
    //요소 생성
    const text = document.createElement("p");
    const body = document.querySelector("body");

    text.innerText = "반가워요!";
    text.style.color = "green";

    body.appendChild(text);
    ```

## 14) 이벤트

- code (click event 등록 & 삭제)

  ```jsx
  const button = document.querySelector("button");
  const removeButton = document.querySelector(".remove");

  function handler(event) {
    console.log(event);
  }

  function removeHandler(event) {
    button.removeEventListener("click", handler);
  }
  //eventListener 등록
  button.addEventListener("click", handler);
  //eventListener 삭제
  removeButton.addEventListener("click", removeHandler);
  ```

## 15) 모듈

- 코드를 모아둔 파일이다. 재사용성이나 유지보수성을 위해 사용된다.
- 모듈 생성법

  - Named Exports

    - 하나의 모듈에서 여러 개의 함수나 객채, 변수 등을 내보낼 수있음.
    - code (index.js)

      ```jsx
      import { greeting } from "./hello.js";

      greeting();
      ```

    - code(hello.js)
      ```jsx
      export function greeting() {
        console.log("hello");
      }
      ```

  - Default Exports

    - code (index.js)

      ```jsx
      import greeting from "./hello.js";

      greeting();
      ```

    - code(hello.js)
      ```jsx
      export default function greeting() {
        console.log("hello");
      }
      ```

## 16) 클래스

- 기존의 자바스크립트에서는 프로토타입 지원함. ES6에서부터 클래스 문법 제공
- code

  ```java
  //class
  class User {
    constructor(name, job) {
      this.name = name;
      this.job = job;
    }
    message() {
      return `이름은 ${this.name}, 직업은 ${this.job}입니다.`;
    }
  }

  console.log(new User("slow", "student").message());
  ```

## 참고 자료

- [[inflearn] **자바스크립트 ES6+ 기초 핵심 문법**](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-es6-%EA%B8%B0%EC%B4%88%ED%95%B5%EC%8B%AC/community?type=question)
- [[blog] **ES6 문법 + 활용 패턴 살펴보기**](https://chancethecoder.tistory.com/30)
- [[inflearn]**자바스크립트 중급 강좌**](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%A4%91%EA%B8%89/dashboard)
