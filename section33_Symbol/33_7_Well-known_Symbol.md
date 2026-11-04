## 33.7 Well-known Symbol

JS가 미리 정의하고 표준 알고리즘에 사용하는 심벌

`Symbol.iterator`처럼 Symbol 함수의 정적 프로퍼티로 제공. 메서드 이름으로 사용되는 경우 **심벌 자체는 함수가 아니라 키**

```js
console.log(typeof Symbol.iterator); // symbol
console.log(typeof [1, 2][Symbol.iterator]); // function

console.log(Symbol.iterator === Symbol.iterator); // true
console.log(Symbol.iterator === Symbol("Symbol.iterator")); // true
console.log(Symbol.iterator === Symbol.for("Symbol.iterator")); // true
```

Well-known Symbol은 `Symbol.for()`로 등록한 심벌과 별개

### 주요 Well-known Symbol

| 심벌                                          | 동작                                    |
| --------------------------------------------- | --------------------------------------- |
| Symbol.iterator                               | for...of, 배열·호출 Spread 등 동기 순회 |
| Symbol.asyncIterator                          | for await...of 등 비동기 순회           |
| Symbol.hasInstance                            | instanceof 판정                         |
| Symbol.isConcatSpreadable                     | concat에 요소를 펼칠치 결정             |
| Symbol.match / Symbol.matchAll / Symbol.split | 대응하는 문자열 메서드 동작             |
| Symbol.toPrimitive                            | 객체를 원시값으로 변환하는 동작         |
| Symbol.toStringTag                            | Object.prototype.toString 태그          |
| Symbol.species                                | 일부 내장 메서드 결과 객체 생성자 선택  |
| Symbol.unscopables                            | with 문 이름 탐색에 제외할 속성 지정    |

### Symbol.iterator, 이터레이션 프로토콜

| 개념            | 조건                                       |
| --------------- | ------------------------------------------ |
| 이터러블        | Symbol.iterator 메서드가 이터레이터를 반환 |
| 이터레이터      | next 메서드가 결과 객체를 반환             |
| 이터레이터 결과 | value와 done으로 값·종료 여부 표현         |

배열·문자열·Map·Set 등은 이터러블. 일반 객체는 기본적으로 이터러블이 아니지만 직접 구현할 수 있음

```js
const iterable = {
  [Symbol.iterator]() {
    let current = 1;
    const max = 5;

    return {
      next() {
        if (current <= max) {
          return { value: current++, done: false };
        }

        return { value: undefined, done: true };
      },
    };
  },
};

const iterator = iterable[Symbol.iterator]();
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }

for (const number of iterable) {
  console.log(number); // 1, 2, 3, 4, 5를 차례대로 출격
}

console.log([...iterable]); // [1, 2, 3, 4, 5]
console.log([...iterable]); // [1, 2, 3, 4, 5]
```

`[Symbol.iterator]()` 호출마다 새 이터레이터를 반환하여 각 순회는 1부터 시작. 이터레이터의 현재 위치는 각 호출의 클로저에 저장

```mermaid
flowchart TD
  A["For...of 또는 배열 Spread"] --> B["Symbol.iterator 호출:]
  B --> C["이터레이터 생성"]
  C --> D["next 호출"]
  D --> E{"done이 true인가"}
  E --> |"아니요"| F["value 사용"]
  F --> D
  E --> |"예"| G["순회 종료"]
```

`for...in`은 문자열 프로퍼티 키를 순회, `for...of`는 이터러블이 제공하는 값을 순회

### Symbol.toPrimitive

객체의 원시값 반환 규칙을 정의

```js
const price = {
  amount: 1000,

  [Symbol.toPrimitive](hint) {
    return hint === "string" ? `${this.amount}원` : this.amount;
  },
};

console.log(String(price)); // '1000원'
console.log(Number(price)); // 1000
console.log(price + 500); // 1500
```

`hint`는 string, number, default 중 하나. 메서드는 객체가 아닌 원시값을 반환

### Symbol.toStringTag

```js
const collection = {
  [Symbol.toStringTag]: "StudyCollection",
};

console.log(Object.prototype.toString.call(collection)); // '[object StudyCollection]'
console.log(typeof collection); // object
```

표시 태그를 지정하는 것이고 객체의 실제 타입을 새 원시 타입으로 바꾸는 기능은 아님

## 질문과 답변

### 1. Symbol은 객체, 원시값인지?

- 변경 불가능한 원시값. `typeof Symbol()`은 `'symbol()'`

### 2. 현재 JavaScript 타입은 몇개인지

- 아니다. BigInt를 포함 원시 타입 7개와 객체 타입 1개, 총 8개다.

### 3. Symbol('id')를 두 번 호출하면 같은 값

- 아니다. 설명이 같아도 각각 새 심벌. 설명은 식별자가 아님.

### 4. new Symbol()을 사용할 수 있는지

- 사용할 수 없음. TypeError가 발생. 래퍼 객체가 필요하면 `Object(symbol)`을 사용

### 5. 심벌의 문자열·숫자·불리언 변환 결과

- `String(symbol)`과 `symbol.toString()`은 가능. 문자열 연결·템플릿 보간 및 숫자 변환 TypeError다. 불리언 변환은 true

### 6. Symbol.for('id')와 Symbol('id')의 차이

- 전자는 레지스트리의 같은 키로 심벌을 공유, 후자는 호출마다 새 심벌을 만듬

### 7. Symbol.keyFor는 description을 반환

- 등록된 심벌 레지스트리 키를 반환. 등록되지 않았다면 설명이 있어도 undefined. 심벌이 아닌 값을 전달하면 TypeError

### 8. 심벌 키에 접근할 때 대괄호를 사용하는 이유

- `object[key]`는 변수 key의 심벌 값을 키로 사용. `object.key`는 문자열 `'key'`를 사용

### 9. 심벌 프로퍼티는 숨겨지는지

- 아님. Object.getOwnPropertySymbols와 Reflect.ownKeys로 찾을 수 있음.

### 10. 객체 Spread와 Object.assign은 심벌 프로퍼티를 복사

- 열거 가능한 자체 심벌 프로퍼티를 복사. 심벌 키라고 자동으로 비열거 프로퍼티가 되는 것은 아님.

### 11. const, Symbol만으로 객체의 상수 프로퍼티 변경을 막는지

- 아니다. const는 변수 재할당을, 심벌 불변성은 심벌 값 자체의 변경을 막음. 객체의 상수 프로퍼티를 보호하려면 Object.freeze 등을 사용.

### 12. JSON.stringify는 심벌을 어떻게 처리

- 심벌 키 프로퍼티와 객체의 심벌 값 프로퍼티는 제외. 배열의 심벌 값은 null, 최상위 심벌은 undefined 반환.

### 13. Symbol.iterator는 함수

- 심벌 값. 객체의 `[Symbol.iterator]` 프로퍼티에 이터레이터를 반환하는 메서드를 구현

- [ECMAScript: Symbol](https://tc39.es/ecma262/multipage/fundamental-objects.html#sec-symbol-objects)
- [MDN: JavaScript 데이터 타입](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Data_structures)
- [MDN: Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
- [MDN: 프로퍼티 열거·소유권](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Enumerability_and_ownership_of_properties)
- [MDN: JSON.stringify](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
- [MDN: Object.freeze](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)
- [MDN: 이터레이션 프로토콜](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
- [React: useState](https://react.dev/reference/react/useState)
