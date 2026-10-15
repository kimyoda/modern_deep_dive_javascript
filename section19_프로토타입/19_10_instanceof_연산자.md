# 19.10 instanceof 연산자

`instanceof` 연산자는 이항 연산자다. 좌변에는 검사할 값, 우변에는 생성자 함수 등을 받는다. 기본 동작은 우변 함수의 `prototype`이 좌변 객체의 프로토타입 체인에 존재하는지 확인하는 것이다. `Symbol.hasInstance`로 판정 동작을 정의할 수도 있으므로 `constructor` 프로퍼티를 단순 비교하는 연산은 아니다.

```text
객체 instanceof 생성자함수
```

**기본 동작에서는 우변 생성자 함수의 `prototype`이 좌변 객체의 프로토타입 체인에 존재하면 `true`, 아니면 `false`로 평가한다.**

**예제 19-46**

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하여 true로 평가
console.log(me instanceof Person); // true
// Object.prototype이 me 객체의 프로토타입 체인 상에 존재해 true로 평가
console.log(me instanceof Object); // true
```

**예제 19-47**

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {};

// 프로토타입 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 체인상에 존재하지 않으므로 false
console.log(me instanceof Person); // false

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재해 true로 평가된다
console.log(me instanceof Object); // true
```

`me`는 `Person` 생성자 함수로 생성되었지만, 이후 프로토타입이 교체되어 `Person.prototype`을 상속하지 않게 되었다. 따라서 `me instanceof Person`은 `false`다. `parent`를 `Person.prototype`에 대입하면 `me instanceof Person`은 `true`가 된다.

**예제 19-48**

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {};

// 프로토타입 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지않음
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 바인딩
Person.prototype = parent;

// Person.prototype이 me 객체의 프로토타입 체인상에 존재해 true
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재해 true로 평가된다
console.log(me instanceof Object); // true
```

**일반적인 `instanceof` 검사는 생성자 함수의 `prototype`이 객체의 프로토타입 체인에 존재하는지 확인한다.**

- `me instanceof Person`: `Person.prototype`이 `me`의 프로토타입 체인에 있는지 확인한다.
- `me instanceof Object`: `Object.prototype`이 `me`의 프로토타입 체인에 있는지 확인한다.

**예제 19-49**

```js
function Person(name) {
  this.name = name;
}
const me = new Person("Lee");

// 일반 생성자의 prototype 체인 검색을 설명하는 학습용 함수
// Symbol.hasInstance, 바인딩된 함수 등 실제 instanceof의 모든 규칙을 구현하지 않음
function isInstanceof(instance, constructor) {
  if (
    instance === null ||
    (typeof instance !== "object" && typeof instance !== "function")
  ) {
    return false;
  }

  let prototype = Object.getPrototypeOf(instance);

  while (prototype !== null) {
    if (prototype === constructor.prototype) {
      return true;
    }
    prototype = Object.getPrototypeOf(prototype);
  }
  return false;
}

console.log(isInstanceof(me, Person)); // true
console.log(isInstanceof(me, Object)); // true
console.log(isInstanceof(me, Array)); // false
```

인스턴스가 참조하는 프로토타입이 현재 `Person.prototype`과 같다면 `constructor` 프로퍼티의 연결이 없어도 `instanceof` 결과에는 영향을 주지 않는다.

**예제 19-50**

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 프로토타입으로 교체할 객체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Lee");

// constructor 프로퍼티와 생성자 함수 간의 연결이 파괴되어도 instanceof는 아무런 영향을 받지 않음
console.log(me.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 체인상에 존재해 true
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재해 true로 평가
console.log(me instanceof Object); // true
```
