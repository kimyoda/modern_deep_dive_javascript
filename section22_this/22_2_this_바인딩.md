# 22.2 함수 호출 방식과 `this` 바인딩

**일반 함수의 `this` 바인딩은 함수를 어떻게 호출했는지에 따라 동적으로 결정된다. 단, 화살표 함수는 자신만의 `this`를 만들지 않는다.**

### 참고: 렉시컬 스코프와 `this` 바인딩

렉시컬 스코프와 일반 함수의 `this` 바인딩은 결정 시점이 다르다. 렉시컬 스코프는 함수가 정의되는 위치에 따라 상위 스코프가 결정되고, 일반 함수의 `this`는 호출 시점의 호출 방식에 따라 결정된다.

동일한 함수도 다음과 같이 다양한 방식으로 호출할 수 있다.

1. 일반 함수 호출
2. 메서드 호출
3. 생성자 함수 호출
4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출

**예제 22-06**

```js
// this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다
const foo = function () {
  console.dir(this);
};

// 동일한 함수도 다양한 방식으로 호출할 수 있음

// 1. 일반 함수 호출
// foo 함수를 일반적인 방식으로 호출
// 브라우저 일반 스크립트(sloppy mode)에서 foo의 this는 window다
foo(); // 브라우저 일반 스크립트: window

// 2. 메서드 호출
// foo 함수를 프로퍼티 값으로 할당하여 호출
// foo 함수 내부의 this는 메서드를 호출한 객체 obj를 가리킨다
const obj = { foo };
obj.foo(); // obj (실제 출력은 객체 표시 형식에 따름)

// 3. 생성자 함수 호출
// foo 함수를 new 연산자와 함께 생성자 함수로 호출
// foo 함수 내부의 this는 생성자 함수가 생성한 인스턴스를 가리킨다
new foo(); // foo {} (생성된 인스턴스)

// 4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출
// foo 함수 내부의 this는 인수에 의해 결정된다
const bar = { name: "bar" };

foo.call(bar); // bar 객체
foo.apply(bar); // bar 객체
const boundFoo = foo.bind(bar); // 새로운 바인딩 함수 반환(원본 foo는 아직 호출되지 않음)
boundFoo(); // bar
```

### 22.2.1 일반 함수 호출

sloppy mode에서 일반 함수를 호출하면 `this`는 전역 객체가 되고, strict mode에서는 `undefined`가 된다. 콜백을 호출하는 API가 별도로 수신 객체를 지정할 수도 있다.

**예제 22-07** (브라우저 일반 스크립트 기준)

```js
function foo() {
  console.log("foo's this: ", this); // window
  function bar() {
    console.log("bar's this: ", this); // window
  }
  bar();
}
foo();
```

**sloppy mode에서 중첩 함수를 일반 함수로 호출하면 `this`는 전역 객체가 된다. strict mode에서는 `undefined`다.**

일반 함수에서 `this`는 메서드처럼 객체를 가리키는 데 쓰이지 않는 경우가 많다. strict mode에서 일반 함수로 호출하면 `this`에 `undefined`가 바인딩된다.

**예제 22-08**

```js
function foo() {
  "use strict";

  console.log("foo's this: ", this); // undefined
  function bar() {
    console.log("bar's this: ", this); // undefined
  }
  bar();
}
foo();
```

이 예제에서는 `foo`와 중첩 함수 `bar`가 모두 일반 함수로 호출되므로 `this`가 `undefined`다.

**예제 22-09** (브라우저 일반 스크립트 기준)

```js
// var 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티다
var value = 1;
// const 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티가 아니다
// const value = 1;
const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // { value: 100, foo: f }
    console.log("foo's this.value: ", this.value); // 100

    // 메서드 내에서 정의한 중첩 함수
    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1
    }

    // 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에 전역 객체가 바인딩된다
    bar();
  },
};

obj.foo();
```

다음 타이머 예제는 브라우저의 일반 스크립트 기준이다. Node.js의 `setTimeout`은 일반 함수 콜백에 `Timeout` 객체를 `this`로 제공하므로 아래 출력값을 Node.js에 그대로 적용하면 안 된다.

**예제 22-10** (브라우저 일반 스크립트 기준)

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // { value: 100, foo: f }

    // 콜백 함수 내부의 this에 전역 객체가 바인딩된다
    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  },
};

obj.foo();
```

### 참고: `setTimeout` 함수

`setTimeout`은 두 번째 인수로 전달한 대기 시간이 지난 후, 이벤트 루프에서 실행 가능한 시점에 첫 번째 인수인 콜백을 호출한다. 정확히 100ms 후 실행되는 것은 보장하지 않는다.

**`this`는 strict mode 여부와 콜백 API의 호출 규칙까지 함께 확인해야 한다. 함수가 중첩되어 있다는 이유만으로 바깥 메서드의 `this`가 전달되지는 않는다.**

메서드 내부의 중첩 함수나 콜백에서 바깥 메서드와 동일한 `this`를 사용하려면 별도의 방법이 필요하다. 다음 예제는 `that` 변수에 바깥쪽 `this`를 저장하는 방법이다.

**예제 22-11**

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    // this 바인딩(obj)을 변수 that에 할당한다
    const that = this;

    // 콜백 함수 내부에 this 대신 that을 참조
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  },
};

obj.foo();
```

자바스크립트는 `Function.prototype.apply`, `Function.prototype.call`, `Function.prototype.bind` 메서드를 제공한다. 콜백에서 `bind`를 사용하는 예제는 다음과 같다.

**예제 22-12**

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 콜백 함수에 명시적으로 this를 바인딩한다
    setTimeout(
      function () {
        console.log(this.value); // 100
      }.bind(this),
      100,
    );
  },
};

obj.foo();
```

또는 화살표 함수를 사용해 바깥쪽 `this`를 그대로 참조할 수 있다.

**예제 22-13**

```js
var value = 1;
const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다
    setTimeout(() => console.log(this.value), 100); // 100
  },
};

obj.foo();
```

### 22.2.2 메서드 호출

메서드 호출에서 `this`는 **메서드를 실제로 호출한 수신 객체**에 바인딩된다. 일반적인 `obj.method()` 형태에서는 마침표 앞의 `obj`가 수신 객체다.

**예제 22-14**

```js
const person = {
  name: "Lee",
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다
    return this.name;
  },
};

// 메서드 getName을 호출한 객체는 person이다
console.log(person.getName()); // Lee
```

`getName` 메서드는 `person` 객체에 정의되어 있다.

`getName`은 함수 값이므로 다른 객체의 프로퍼티에 할당하거나 일반 변수에 저장할 수 있다. 이때 실제 호출 형태에 따라 `this`가 달라진다.

**예제 22-15**

```js
const anotherPerson = {
  name: "Kim",
};

// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다
console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName을 일반 함수로 호출(브라우저 일반 스크립트 기준)
console.log(getName()); // 브라우저 일반 스크립트: window.name(대개 "")
// Node.js CommonJS의 sloppy mode에서는 globalThis.name(대개 undefined)을 반환
// strict mode에서는 this가 undefined여서 TypeError 발생
```

메서드를 일반 함수로 분리해 호출하면 객체를 수신자로 전달하던 연결이 사라진다.

프로토타입 메서드에서도 `this`는 메서드를 실제로 호출한 수신 객체를 가리킨다.

**예제 22-16**

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Lee");

// getName 메서드를 호출한 객체는 me
console.log(me.getName()); // Lee

Person.prototype.name = "Kim";

// getName 메서드를 호출한 객체는 Person.prototype이다
console.log(Person.prototype.getName()); // Kim
```

### 22.2.3 생성자 함수 호출

생성자 함수를 `new`로 호출하면 `this`에 새로 생성 중인 인스턴스가 바인딩된다.

**예제 22-17**

```js
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킴
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 반지름이 5인 Circle 객체를 생성
const circle1 = new Circle(5);
// 반지름이 10인 Circle 객체를 생성
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

생성자 함수는 객체를 생성하는 데 사용하는 함수다. 일반 함수를 `new` 없이 호출하면 일반 함수 호출 규칙이 적용된다. 다음 예제는 **22-17의 `Circle` 정의를 먼저 실행한 뒤**, 브라우저 일반 스크립트 환경에서 이어서 실행하는 코드다.

**예제 22-18** (예제 22-17에 이어서 실행)

```js
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않음. 일반적인 함수의 호출
const circle3 = Circle(15);

// 일반 함수로 호출된 Circle은 명시적 반환문이 없어 undefined를 반환
console.log(circle3); // undefined

// 브라우저 일반 스크립트(sloppy mode)에서는 전역 객체를 가리킴
console.log(radius); // 15 (브라우저 일반 스크립트의 암묵적 전역)
```

### 22.2.4 Function.prototype.apply/call/bind 메서드에 의한 간접 호출

`apply`, `call` 메서드의 사용법은 다음과 같다.

**apply**

```text
/**
 * 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출한다
 * @param thisArg - this로 사용할 객체
 * @param argsArray - 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
 * @returns 호출된 함수의 반환값
*/
Function.prototype.apply(thisArg[, argsArray])
```

**call**

```text
/**
 * 주어진 this 바인딩과 쉼표로 구분된 인수 목록을 사용하여 함수를 호출한다
 * @param thisArg - this로 사용할 객체
 * @param arg1, arg2, ... - 함수에게 전달할 인수 리스트
 * @returns 호출된 함수의 반환값
*/
Function.prototype.call(thisArg[, arg1[, arg2[ ...]]])
```

**예제 22-19** (일반 함수의 `this` 출력은 실행 환경에 따라 다름)

```js
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

console.log(getThisBinding()); // 브라우저의 일반 스크립트: window

// thisArg를 함수 내부의 this로 바인딩한다
console.log(getThisBinding.apply(thisArg)); // { a: 1}
console.log(getThisBinding.call(thisArg)); // { a: 1}
```

**`apply`와 `call`은 즉시 함수를 호출한다.** 첫 번째 인수로 `this`에 바인딩할 값을 전달한다. 단, strict mode와 함수의 종류(화살표 함수 등)에 따라 `this` 처리 규칙이 다를 수 있다.

**예제 22-20**

```js
function getThisBinding() {
  console.log(arguments);
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// thisArg를 함수 내부의 this로 바인딩한다
// apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// arguments 객체에 1, 2, 3이 들어 있다(출력 형식은 실행 환경에 따라 다름)
// { a: 1 }

// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// arguments 객체에 1, 2, 3이 들어 있다(출력 형식은 실행 환경에 따라 다름)
// { a: 1 }
```

`apply`는 인수를 배열 또는 유사 배열 객체로 전달하고 `call`은 쉼표로 구분한 인수 목록을 전달한다. `arguments`는 배열이 아닌 유사 배열 객체이므로 배열 메서드를 직접 호출할 수 없다. 그러나 `Array.prototype.slice.call(arguments)`처럼 간접 호출하면 배열로 변환할 수 있다.

**예제 22-21**

```js
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수 없이 호출하면 배열의 복사본을 생성한다
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

`Function.prototype.bind`는 원본 함수를 즉시 호출하지 않고 지정한 `this`와 일부 인수를 기억하는 새로운 함수를 반환한다.

**예제 22-22**

```js
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// bind 메서드는 함수에 this로 사용할 객체를 전달한다
// bind 메서드는 함수를 호출하지 않음
console.log(getThisBinding.bind(thisArg)); // 바인딩된 새 함수가 출력됨
// bind 메서드는 함수를 호출하지 않으므로 명시적으로 호출해야 한다
console.log(getThisBinding.bind(thisArg)()); // { a: 1 }
```

`bind`는 메서드와 콜백 함수의 `this`가 달라지는 문제를 해결할 때 유용하다.

**예제 22-23** (브라우저 일반 스크립트 기준 출력 설명)

```js
const person = {
  name: "Lee",
  foo(callback) {
    // 1
    setTimeout(callback, 100);
  },
};

person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // window.name이 빈 문자열이면: Hi! my name is .
  // 브라우저 일반 스크립트에서는 콜백의 this.name이 window.name을 참조
  // window.name은 창 이름이므로 기존 페이지에서 값이 설정되어 있을 수 있다.
});
```

`foo` 메서드 내부에서의 `this`는 `person`이다. 그러나 `setTimeout`에 전달한 일반 함수 콜백의 `this`는 브라우저 일반 스크립트 기준으로 `window`다. 따라서 콜백이 `person.name`을 사용하도록 하려면 `bind(this)` 등으로 수신 객체를 지정해야 한다. Node.js에서는 일반 함수 타이머 콜백의 `this`가 `Timeout` 객체라는 점도 주의한다.

**예제 22-24**

```js
const person = {
  name: "Lee",
  foo(callback) {
    // bind 메서드로 callback 함수 내부의 this 바인딩 전달
    setTimeout(callback.bind(this), 100);
  },
};

person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // Hi! my name is Lee.
});
```

## apply, call, bind 비교

| 메서드 | 호출          | 인수 전달 방식              | 반환      |
| ------ | ------------- | --------------------------- | --------- |
| call   | 호출          | 인수를 하나씩 나열          | 호출 결과 |
| apply  | 호출          | 배열 또는 유사 배열 객체    | 호출 결과 |
| bind   | 호출하지 않음 | this, 일부 인수를 미리 지정 | 새 함수   |

```js
function introduce(greeting) {
  return greeting + ", " + this.name;
}

const user = { name: "Kim" };
console.log(introduce.call(user, "안녕")); // 안녕, Kim
console.log(introduce.apply(user, ["안녕"])); // 안녕, Kim

const boundIntroduce = introduce.bind(user, "안녕");
console.log(boundIntroduce()); // 안녕, Kim
```

### 메서드를 변수로 분리하면 수신 객체가 사라진다

```js
"use strict";

const user = {
  name: "Kim",
  getName() {
    return this.name;
  },
};

const detached = user.getName;

try {
  detached();
} catch (error) {
  console.log(error.name); // TypeError: this가 undefined
}

const bound = user.getName.bind(user);
console.log(bound()); // Kim
```

클로저가 상위 스코프의 변수를 기억하는 것과 함수가 수신 객체를 `this`로 받는 것은 다른 규칙이다.

### 화살표 함수는 바깥 this를 사용

```js
const user = {
  name: "Kim",
  createReader() {
    return () => this.name;
  },
};

const read = user.createReader();
console.log(read.call({ name: "Lee" })); // Kim
```

객체 리터럴의 메서드를 무조건 화살표 함수로 바꾸면 바깥 this를 사용해 의도와 다른 결과가 날 수 있다. 객체 자신의 상태를 `this`로 읽는 일반 메서드와, 그 메서드 안에서 생성한 콜백을 구분해야 한다.

### 브라우저, Node.js 타이머를 구분

Node.js에서 일반 함수 타이머 콜백의 `this`는 `Timeout` 객체다. 화살표 함수는 자신만의 `this`를 만들지 않으므로 바깥쪽 `this`를 사용한다.

```js
// Node.js에서 실행
const user = {
  value: 100,
  schedule() {
    setTimeout(function () {
      console.log(this === user); // false
    }, 0);

    setTimeout(() => {
      console.log(this === user); // true
      console.log(this.value); // 100
    }, 0);
  },
};

user.schedule();
```

## 복습 질문 및 답변

1. **메서드가 정의된 객체가 항상 this인가**
   아니다. 실제 호출의 수신 객체가 기준
2. **strict 일반 호출에 this는 무엇인가**
   undefined
3. **중첩 함수의 this는 바깥 메서드의 this와 같은지**
   일반 함수로 호출하면 자동으로 같아지지 않음
4. **메서드를 변수에 저장해 호출하면 무엇이 달라지는지**
   객체를 수신자로 호출하던 관계가 사라진다
5. **apply, call의 차이는 무엇인가**
   apply는 인수를 배열로, call은 개별 인수로 전달한다
6. **bind는 함수를 바로 실행하는지**
   아니다. `bind`는 `this`와 일부 인수를 고정한 새 함수를 반환할 뿐 즉시 실행하지 않는다.
7. **화살표 함수에 call을 쓰면 this가 바뀌는지**
   바뀌지 않는다. 화살표 함수는 자신만의 `this`를 만들지 않고 바깥쪽의 `this`를 참조한다.
8. **콜백의 this는 언제나 전역 객체인지?**
   아니다. strict 여부와 콜백 API의 호출 규칙에 따라 다르다
