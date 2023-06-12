---
title: "[javascript] ES6+ 핵심 문법 정리 2"
date: 2023-06-12T23:11:29+09:00
draft: false
tags: ["Javascript"]
categories: ["Javascript"]
showToc: true
UseHugoToc: true
comments: true
---

## 0) Intro

javascript 문법 공부를 위해 Inflearn에 있는 강의를 듣고 정리한 글입니다.

- [코딩앙마님의 자바스크립트 중급 강좌](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%A4%91%EA%B8%89/dashboard)

## 1) 나머지 매개변수와 전개 구문

### arguments

- 함수로 넘어 온 모든 인수에 접근
- 함수내에서 이용가능한 지역 변수
- Array 형태의 객체
- 배열의 내장 메서드가 없음. (forEach, map)
- arguments.length → arguments의 갯수 반환
- code
  ```jsx
  function showName(name) {
    console.log(arguments.length);
    console.log(arguments[0]);
    console.log(arguments[1]);
  }
  showName("slow", "wave");
  //2
  //slow
  //wave
  ```

### 나머지 매개변수(Rest parameters)

- ES6+부터는 나머지 매개변수 사용을 권장하고 있음.
- code
  ```jsx
  //나머지 매개변수
  function Movie(id, rating, ...tags) {
    this.id = id;
    this.rating = rating;
    this.tags = tags;
  }
  const movie1 = new Movie(1, 5, "fun", "action");
  const movie2 = new Movie(2, 3, "sad", "drama", "good");
  console.log(movie1); //Movie {id: 1, rating: 5, tags: Array(2)}
  console.log(movie2); //Movie {id: 2, rating: 3, tags: Array(3)}
  ```

### 전개구문(Spread syntax)

- code
  ```jsx
  let movie = { name: "avatar" };
  let info = { id: 1 };
  let type = ["action", "fantasy"];
  let emotion = ["fun", "exciting"];
  movie = {
    ...movie,
    ...info,
    tags: [...type, ...emotion],
  };
  console.log(movie); //{name: 'avatar', id: 1, tags: Array(4)}
  ```

## 2) 클로저(closure)

### 호이스팅

- 인터프리터가 변수와 함수의 메모리 공간을 선언 전에 미리 할당하는 것을 의미함.
- 변수의 선언과 초기화를 분리한 후, 선언만 코드의 최상단으로 옮기는 것이라고 할 수 있음.
- JavaScript는 함수의 코드를 실행하기 전에 함수 선언에 대한 메모리부터 할당
  → 함수를 호출하는 코드를 함수 선언보다 앞서 배치할 수 있습니다.

### closure

- 함수와 렉시컬 환경의 조합임.
- 함수가 생성될 당시의 외부 변수를 기억해서 생성 이후에도 계속 접근 가능함.
- code
  ```jsx
  function makeCounter() {
    let num = 0; //은닉화

    return function () {
      return num++;
    };
  }

  let counter = makeCounter();
  console.log(counter()); //0
  console.log(counter()); //1
  console.log(counter()); //2
  ```

## 3) setTimeout, setInterval, clearTimeout

### setTimeout

### setInterval, clearTimeut

- code
  ```jsx
  let s = 5;

  function countdown() {
    console.log(`5초 카운트다운! ${s--}초`);
    if (s < 0) clearInterval(tId);
  }

  const tId = setInterval(countdown, 1000);
  ```

## 4) call, apply, bind

- 함수 호출 방식과 관계없이 this를 지정할 수 있음.

### call

- call 메서드는 모든 함수에서 사용할 수 있으며, this를 특정값으로 지정할 수 있음.
- code
  ```jsx
  const mike = {
    name: "Mike",
  };

  function showThisName() {
    console.log(this.name);
  }
  // showThisName(); //TypeError
  showThisName.call(mike); //Mike

  function update(birthYear, occupation) {
    this.birthYear = birthYear;
    this.occupation = occupation;
  }

  update.call(mike, 1999, "singer");
  console.log(mike); //{name: 'Mike', birthYear: 1999, occupation: 'singer'}
  ```

### apply

- apply는 함수 매개변수를 처리하는 방법을 제외하면 call과 완전히 같음.
- call은 일반적인 함수와 마찬가지로 매개변수를 직접 받지만, **apply는 매개변수를 배열**로 받음.
- code
  ```jsx
  const nums = [4, 2, 1, 2, 10];
  const minNum = Math.min.apply(null, nums); //this가 필요하지 않아서 임의로 null 넣음.
  const maxNum = Math.max.call(null, ...nums);
  console.log(minNum, maxNum); //1 10
  ```

### bind

- 함수의 this값을 영구히 바꿀 수 있음.
- code
  ```jsx
  const user = {
    name: "mike",
    showName: function () {
      console.log(`hello, ${this.name}`);
    },
  };

  user.showName();
  let fn = user.showName;
  fn.call(user);
  fn.apply(user);

  let boundFn = fn.bind(user);
  boundFn(); //hello, mike
  ```

## 5) 상속, prototype

### prototype

- code

```jsx
const user = {
  name: "slow",
};

console.log(user.hasOwnProperty("name")); //true
console.log(user.hasOwnProperty("birth")); //false
```

### prototype chain

- 아래 코드에서 meatPizza와 potatoesPizza에는 공통된 부분이 존재함. (shape, sauce)
  → 중복된 코드 → prototype chain을 이용해 개선함.
- code

```jsx
const meatPizza = {
  shape: "round",
  sauce: "tomato",
  topping: ["meat"],
  time: 10,
};

const potatoesPizza = {
  shape: "round",
  sauce: "tomato",
  topping: ["potatoes"],
  time: 8,
};
```

- code

```jsx
//공통된 부분은 pizza에 넣어줌.
const pizza = {
  shape: "round",
  sauce: "tomato",
};

const meatPizza = {
  topping: ["meat"],
  time: 10,
};
//prototype chain
meatPizza.__proto__ = pizza;
console.log(meatPizza);

console.log(Object.keys(meatPizza)); //["topping",'time"]
console.log(Object.values(meatPizza)); //[["meat"],10]
```

### 생성자 함수

- instance
  - 생성자함수가 새로운 객체 생성할 때 그 객체는 생성자의 인스턴스라고 함.
  - instanceof → 인스턴스인지 확인 가능
- constructor

  - 생성자로 만들어진 인스턴스 객체의 경우 constructor가 존재함.
  - 프로로타입을 겹쳐 쓰지 않고 하나씩 풀어쓰는 것이 좋은 방법임.
  - 자바스크립트는 명확한 constructor를 보장하지 않음.

- code (prototype을 하나씩 풀어쓴 경우 → constructor 존재함.)
  ```jsx
  const Bmw = function (color) {
    this.color = color;
  };

  Bmw.prototype.wheels = 4;
  Bmw.prototype.drive = function () {
    console.log("drive...");
  };

  const x5 = new Bmw("red");
  const z4 = new Bmw("blud");

  console.log(x5.drive); //drive...
  console.log(x5 instanceof Bmw); //true
  console.log(x5.constructor === Bmw); //true
  ```
- code (prototype을 겹쳐서 쓴 경우 → constructor가 존재하지 않음.)
  ```jsx
  const Bmw = function (color) {
    this.color = color;
  };

  Bmw.prototype = {
    wheels: 4,
    drive() {
      console.log("dirve...");
    },
  };

  const x5 = new Bmw("red");
  const z4 = new Bmw("blue");

  console.log(x5.drive); //drive...
  console.log(x5 instanceof Bmw); //true
  console.log(x5.constructor === Bmw); //false
  ```
- code
  ```jsx
  const pizza = function (sauce) {
    this.sauce = sauce;
  };

  const p1 = new pizza("tomato");
  p1.sauce = "cream";
  console.log(p1.sauce); //cream으로 변경됨.
  ```
- code
  ```jsx
  //closure 사용해서 변경되지 않도록 함.
  //초기에 세팅한 값만 얻을 수 있고, 바꿀 수는 없음.

  const pizza = function (sauce) {
    const s = sauce;
    this.getSauce = function () {
      console.log(s);
    };
  };

  const p2 = new pizza("tomato");
  console.log(p2.getSauce()); //tomato
  ```

## promise

- callback 함수 - 어떤 일이 완료된 이후에 실행되는 함수임.
- code
  ```jsx
  //성공시 -> resolve, 실패시 -> reject
  const pr = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("ok");
    }, 3000);
  });

  pr.then(function (result) {
    //성공시 실행
    console.log(result + "가지러 가자.");
  })
    .catch(function (err) {
      //실패시 실행
      console.log("다시 주문해주세요.");
    })
    .finally(function () {
      //항상 실행
      console.log("주문 끝");
    });
  ```

## async/await

- code
  ```jsx
  const f1 = () => {
    console.log();
    return new Promise((res, rej) => {
      setTimeout(() => {
        res("1번");
      }, 1000);
    });
  };

  const f2 = (message) => {
    console.log(message);
    return new Promise((res, rej) => {
      setTimeout(() => {
        res("2번");
      }, 1000);
    });
  };

  const f3 = (message) => {
    console.log(message);
    return new Promise((res, rej) => {
      setTimeout(() => {
        res("3번");
      }, 1000);
    });
  };

  //프로미스 체이닝
  console.log("시작");
  f1()
    .then((res) => f2(res))
    .then((res) => f3(res))
    .then((res) => console.log(res))
    .catch()
    .finally(() => {
      console.log("끝");
    });
  ```

## 참고자료

- [[docs] 호이스팅](https://developer.mozilla.org/ko/docs/Glossary/Hoisting)
- [[inflearn]**자바스크립트 중급 강좌**](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%A4%91%EA%B8%89/dashboard)
