---
layout: post
title: "es6-for-humans"
date: 2016-07-21 14:23:09 +0000
categories: javascript
tags: [es6]
---

# ES6 for Humans

개인적으로 ES6를 다시 한번 배워보는 좋은 기회라 생각되어, 번역을 처음으로 시도해봅니다. 미숙하지만…
번역이 미흡하거나 하면 댓글이나 GitHub issue, PR 모두 환영합니다.

[https://github.com/scarfunk/ES6-for-humans/tree/korean-version](https://github.com/scarfunk/ES6-for-humans/tree/korean-version)

주석 부분은 개인적으로 작성한 추가 설명 부분입니다.
원 출처(origin) 는 [여기](https://github.com/metagrover/ES6-for-humans) 입니다.

## Table of Contents

1. let, const and block scoping
2. Arrow Functions
3. Default Function Parameters
4. Spread/Rest Operator
5. Object Literal Extensions
6. Octal and Binary Literals
7. Array and Object Destructuring
8. super in Objects
9. Template Literal and Delimiters
10. for…of vs for…in
11. Map and WeakMap
12. Set and WeakSet
13. Classes in ES6
14. Symbol
15. Iterators
16. Generators
17. Promises

## 1. let, const and block scoping

`let` 변수 할당은 어떤 block 스코프에도 선언될 수 있습니다. `var`를 사용하는 것 대신에요. 함수 스코프에도 제공됩니다. ES6에서는 `let`을 사용하는 것을 추천합니다.

```javascript
var a = 2;
{
  let a = 3;
  console.log(a); // 3
  let a = 5; // TypeError: Identifier 'a' has already been declared
}
console.log(a); // 2 (블록 스코핑으로 동작합니다)
```

이번엔 `const`입니다. 상수를 생성합니다. ES6에서, `const`는 value의 상수 참조를 표현합니다. 다르게 말해, value는 mutable(불변) 합니다. primitive 타입들은, 할당시에 call by value 방식이므로 그 자체가 복제되어 할당됩니다. 하지만 reference 타입들은 call by reference이므로, `var b = a` 일 때 a의 변경이 b에도 영향을 미칩니다.

```javascript
{
  const B = 5;
  B = 10; // TypeError: Assignment to constant variable

  const ARR = [5, 6];
  ARR.push(7);
  console.log(ARR); // [5,6,7]
  ARR = 10; // TypeError: Assignment to constant variable
  ARR[0] = 3; // value is mutable
  console.log(ARR); // [3,6,7]
}
```

기억해 둘 점:

- `let`과 `const`의 호이스팅은, 기존의 방식과 다릅니다. 둘 다 호이스팅 되지만, 선언 전에는 접근 할 수 없습니다. Temporal Dead Zone 때문입니다.
- `let` 과 `const`는 가장 가까운 블록에 스코핑을 가집니다.
- `const`를 사용할 때, 대문자 변수를 사용하세요.
- `const`는 선언과 동시에, 할당(정의)되어야 합니다.

```javascript
console.log(x); // throws a ReferenceError
let x = 'hey';
```

## 2. Arrow Functions

Arrow Functions 은 ES6에서 축약된 함수를 사용할 때 쓰입니다. 이것은 파라미터의 리스트 `( ... )` 부분과 바로 뒤따르는 `=>` 가 함수의 body 부분으로 이루어져 있습니다.

```javascript
// Classical Function Expression(ES6 이전 방식)
let addition = function(a, b) {
  return a + b;
};

// Arrow Function 으로 구현
let addition = (a, b) => a + b;
```

위의 예제에서 눈여겨볼 부분은, "간결화된 body" 를 사용할 수 있다는 점입니다. 이것은 return 구문이 필요가 없습니다.

`{}`를 이용한 "블록 body"의 예제는 아래와 같습니다.

```javascript
let arr = ['apple', 'banana', 'orange'];

let breakfast = arr.map(fruit => {
  return fruit + 's'; //return 이 필요합니다.
});

console.log(breakfast); // ['apples', 'bananas', 'oranges']
```

더 알아야 할것들…

Arrow function 은 그저 축약을 위한것만이 아닙니다. `this` 바인딩과 밀접한 연관이 있습니다.

Arrow function 에서의 `this`의 작동법은 이전과 다릅니다. 자바스크립트에서 각 function 은 정의 될때 마다, 새로운 `this`를 가집니다. 하지만, Arrow 에서는 `this`를 context 영역에서 고유하게 가집니다.

```javascript
function Person() {
  // The Person() 생성자가 `this` 를 정의합니다
  this.age = 0;

  setInterval(function growUp() {
    // In non-strict mode 에서, the growUp() `this`를 global 에 정의해 버립니다.
    this.age++;
  }, 1000);
}

var p = new Person(); // this.age는 NaN 입니다.
```

ECMAScript 3/5 에서는, 이 이슈를 `this`를 다른변수에 할당하는방법으로 해결 했었습니다.

```javascript
function Person() {
  var self = this;
  self.age = 0;

  setInterval(function growUp() {
    // 콜백 펑션은 'self' 변수를 객체안에서 참조 합니다.
  }, 1000);
}

var p = new Person(); // age++ 은 올바르게 증가합니다
```

Arrow function 은 context 안에서 value 를 잡아두고 있는것이죠. 그래서 아래 코드는 잘 작동 합니다.

```javascript
function Person() {
  this.age = 0;

  setInterval(() => {
    this.age++; // `this` properly refers to the person object
  }, 1000);
}

var p = new Person();
```

## 3. Default Function Parameters

ES6은 디폴트 파라미트를 지원합니다.

```javascript
let getFinalPrice = (price, tax = 0.7) => price + price * tax;
getFinalPrice(500); // 850
```

## 4. Spread / Rest Operator

`...` 연산자는 사용방식에 따라, "spread" 혹은 "rest" 연산으로 나뉩니다.

어떤 iterable 한 객체에 사용될때, 각각의 elements 에 알맞게 나눠준다고 할수 있습니다. 이것을 "spread" 연산이라고 합니다.

```javascript
function foo(x, y, z) {
  console.log(x, y, z);
}

let arr = [1, 2, 3];
foo(...arr); // 1 2 3
```

다른 또 하나는 여러개의 밸류를 하나의 array 로 모으는 방식이 있습니다. 이것을 "rest" 연산이라고 합니다.

```javascript
function foo(...args) {
  console.log(args);
}
foo(1, 2, 3, 4, 5); // [1, 2, 3, 4, 5]
```

## 5. Object Literal Extensions

ES6는 객체에 변수에 속성을 초기화 하거나, 함수 방법을 정의하는 것에 축약 구문을 제공합니다.

```javascript
function getCar(make, model, value) {
  return {
    // 프로퍼티 축약 구문
    make,  // make: make 과 같습니다
    model, // model: model 과 같습니다
    value, // value: value 과 같습니다

    // computed key
    ['make' + make]: true,

    // 함수 정의도 축약이 가능합니다
    depreciate() {
      this.value -= 2500;
    }
  };
}

let car = getCar('Kia', 'Sorento', 40000);
// {
//   make: 'Kia',
//   model: 'Sorento',
//   value: 40000,
//   makeKia: true,
//   depreciate: function()
// }
```

## 6. Octal and Binary Literals

ES6는 8진수 및 2진수 에 대한 새로운 기능을 지원하고 있습니다.

```javascript
let oValue = 0o10;
console.log(oValue); // 8

let bValue = 0b10;
console.log(bValue); // 2
```

## 7. Array and Object Destructuring

Destructuring 은 객체 및 배열을 처리 할 때 임시변수의 사용을 피할때 사용됩니다.

```javascript
function foo() {
  return [1, 2, 3];
}
let arr = foo(); // [1,2,3]

let [a, b, c] = foo();
console.log(a, b, c); // 1 2 3

function bar() {
  return { x: 4, y: 5, z: 6 };
}
let { x: a, y: b, z: c } = bar();
console.log(a, b, c); // 4 5 6
```

## 8. super in Objects

ES6는 (클래스가 아닌)객체에 프로토타입 `super` 함수을 사용할 수 있습니다.

```javascript
var parent = {
  foo() {
    console.log("Hello from the Parent");
  }
}

var child = {
  foo() {
    super.foo();
    console.log("Hello from the Child");
  }
}

Object.setPrototypeOf(child, parent);
child.foo(); // Hello from the Parent
             // Hello from the Child
```

## 9. Template Literal and Delimiters

ES6의 문자열을 쉽게 만드는 방법입니다. `` `${ … }` `` 가 변수의 랜더링에 사용됩니다. Backtick(백틱) 이 구분기호로 사용됩니다.

```javascript
let user = 'Kevin';
console.log(`Hi ${user}!`); // Hi Kevin!
```

## 10. for…of vs for…in

`for...of` 는 배열같은 iterable 객체를 순환 합니다.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname of nicknames) {
  console.log(nickname);
}
// di
// boo
// punkeye
```

`for...in` 는 모든 객체속의 열거형 프로퍼티를 순환합니다.

```javascript
let nicknames = ['di', 'boo', 'punkeye'];
nicknames.size = 3;
for (let nickname in nicknames) {
  console.log(nickname);
}
// 0  (for...of 와 다르게, value 가 아닌, key 값)
// 1
// 2
// size
```

## 11. Map and WeakMap

ES6 은 Map 과 WeakMap 이라는 새로운 데이터 구조가 있습니다. Map에서는 어떤 밸류든(객체 혹은 원시 밸류) 키와 밸류로 사용될수 있습니다.

```javascript
var myMap = new Map();

var keyString = "a string",
    keyObj = {},
    keyFunc = function() {};

myMap.set(keyString, "value associated with 'a string'");
myMap.set(keyObj, "value associated with keyObj");
myMap.set(keyFunc, "value associated with keyFunc");

myMap.size; // 3

myMap.get(keyString); // "value associated with 'a string'"
myMap.get(keyObj);    // "value associated with keyObj"
myMap.get(keyFunc);   // "value associated with keyFunc"
```

**WeakMap** 은 키들이 약한 참조를 가지고 있습니다. 이것은 키들이 GC 되는것을 막지 못합니다. WeakMap은 항상 key는 객체여야 합니다.

```javascript
let w = new WeakMap();
w.set('a', 'b');
// Uncaught TypeError: Invalid value used as weak map key

var o1 = {}, o2 = function(){}, o3 = window;

w.set(o1, 37);
w.set(o2, "azerty");
w.set(o3, undefined);

w.get(o3); // undefined, because that is the set value

w.has(o1); // true
w.delete(o1);
w.has(o1); // false
```

## 12. Set and WeakSet

Set객체는 고유한 값의 컬렉션입니다. 중복된 밸류는 무시됩니다.

```javascript
let mySet = new Set([1, 1, 2, 2, 3, 3]);
mySet.size; // 3
mySet.has(1); // true
mySet.add('strings');
mySet.add({ a: 1, b: 2 });
```

`forEach` 함수나 `for...of` 루프로 순환 할수 있습니다.

```javascript
mySet.forEach((item) => {
  console.log(item);
});

for (let value of mySet) {
  console.log(value);
}
```

**WeakSet** 은 WeakMap 과 비슷하게, 객체들을 약하게 참조합니다.

```javascript
var ws = new WeakSet();
var obj = {};
var foo = {};

ws.add(window);
ws.add(obj);

ws.has(window); // true
ws.has(foo);    // false

ws.delete(window);
ws.has(window); // false
```

## 13. Classes in ES6

ES6 는 클래스 구문이 있습니다. 여기서 주의 할 점은 ES6의 클래스는 새로운 객체 지향 상속 모델이 아니라는 것입니다. 단지 자바 스크립트 의 기존 프로토 타입 기반 상속의 새로운 구문입니다.

```javascript
class Task {
  constructor() {
    console.log("task instantiated!");
  }

  showId() {
    console.log(23);
  }

  static loadAll() {
    console.log("Loading all tasks..");
  }
}

console.log(typeof Task); // function
let task = new Task(); // "task instantiated!"
task.showId(); // 23
Task.loadAll(); // "Loading all tasks.."
```

**extends and super in classes**

```javascript
class Car {
  constructor() {
    console.log("Creating a new car");
  }
}

class Porsche extends Car {
  constructor() {
    super();
    console.log("Creating Porsche");
  }
}

let c = new Porsche();
// Creating a new car
// Creating Porsche
```

`extends` 는 자식 클래스가 부모로 부터 상속을 받을수 있게 합니다. 자식의 생성자는 반드시 `super()` 메소드를 호출해야 합니다.

## 14. Symbol

ES6의 심볼은 unique 하며, 불가변적인 데이터 타입 입니다.

```javascript
var sym = Symbol("some optional description");
console.log(typeof sym); // symbol
```

심볼이 객체의 프로퍼티/키 로 사용된다면, 보통의 enumeration 탐색으로는 나타나지 않습니다.

```javascript
var o = {
  val: 10,
  [Symbol("random")]: "I'm a symbol",
};

console.log(Object.getOwnPropertyNames(o)); // val
```

객체의 심볼 프로퍼티를 찾기 위해서는, `Object.getOwnPropertySymbols(o)` 를 사용하세요.

## 15. Iterators

iterator 는 현재의 시퀀스를 가지고 위치를 추적하면서, collection 의 아이템을 한번에 하나씩 접근합니다. `next()` 메소드는 다음 순서의 아이템을 반환하며, 리턴된 객체는 2개의 프로퍼티를 가집니다: `done` 과 `value`.

```javascript
var arr = [11, 12, 13];
var itr = arr[Symbol.iterator]();

itr.next(); // { value: 11, done: false }
itr.next(); // { value: 12, done: false }
itr.next(); // { value: 13, done: false }
itr.next(); // { value: undefined, done: true }
```

## 16. Generators

제네레이터 function 은 ES6의 신기능 중 하나이며, 함수가 많은 값들을 iterable 하게 순환하며 꺼내올수 있는 방법을 가능하게 합니다.

```javascript
function *infiniteNumbers() {
  var n = 1;
  while (true) {
    yield n++;
  }
}

var numbers = infiniteNumbers(); // returns an iterable object

numbers.next(); // { value: 1, done: false }
numbers.next(); // { value: 2, done: false }
numbers.next(); // { value: 3, done: false }
```

`yield` 가 호출될때 마다, yielded(산출된) 값이 다음 순차의 value 에 나오게 됩니다.

## 17. Promises

ES6는 promise 를 지원합니다. promise는 비동기적인 작업이 완료되는것을 기다려주는 객체입니다. 작업이 완료 되면, promise 는 fulfilled(resolved) 혹은 rejected(거부) 가 됩니다.

```javascript
var p = new Promise(function(resolve, reject) {
  if (/* condition */) {
    resolve(/* value */);  // fulfilled successfully
  } else {
    reject(/* reason */);  // error, rejected
  }
});
```

모든 Promise 객체는 `then` 메소드를 가지며, 두개의 콜백함수를 파라미터로 받습니다.

```javascript
p.then(
  (val) => console.log("Promise Resolved", val),
  (err) => console.log("Promise Rejected", err)
);
```

`then` 콜백들에서 반환된 값은 다음 `then` 콜백으로 전달 됩니다.

```javascript
var hello = new Promise(function(resolve, reject) {
  resolve("Hello");
});

hello.then((str) => `${str} World`)
     .then((str) => `${str}!`)
     .then((str) => console.log(str)) // Hello World!
```

Promise chaining을 통해 "콜백 지옥"을 피할 수 있습니다.

```javascript
var p = new Promise(function(resolve, reject) {
  resolve(1);
});

var eventuallyAdd1 = (val) => {
  return new Promise(function(resolve, reject) {
    resolve(val + 1);
  });
};

p.then(eventuallyAdd1)
 .then(eventuallyAdd1)
 .then((val) => console.log(val)) // 3
```
