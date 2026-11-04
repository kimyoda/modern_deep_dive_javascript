## 33.2 심벌 값의 생성

### 33.2.1 Symbol 함수

#### 기본 생성과 new 사용 금지

심벌 전용 리터럴 문법은 없음. `Symbol()`로 새로운 값을 만듬.

```js
const mySymbol = Symbol();

console.log(typeof mySymbol); // symbol
console.log(String(mySymbol)); // `Symbol()`

try {
  new Symbol();
} catch (error) {
  console.log(error.name); // TypeError
}
```

`Symbol()`은 원시값을 반환. `new Symbol()`로 인스턴스를 만드는 방식은 허용되지 않음.

표시 문자열이 심벌의 고유 식별자 역할을 하지는 않음

#### 설명은 식별자가 아님

```js
const first = Symbol("mySymbol");
const second = Symbol("mySymbol");

console.log(first.description); // 'mySymbol'
console.log(first.toString()); // 'Symbol(mySymbol)'
console.log(first === second); // false

console.log(Symbol().description); // undefined
console.log(Symbol("").description); // ''
```

`description`은 디버깅용 설명을 읽는 프로퍼티다

#### 래퍼 객체

심벌도 프로퍼티에 접근하면 래퍼 객체를 통한 접근이 가능. 명시적으로 래퍼를 만들 때 `Object(symbol)`을 사용.

```js
const symbol = Symbol("token");
const wrapped = Object(symbol);

console.log(typeof symbol); // symbol
console.log(typeof wrapped); // object
console.log(wrapped.valueOf() === symbol); // true
```

#### 타입 변환

| 표현                      | 결과                 |
| ------------------------- | -------------------- |
| String(symbol)            | 설명을 포함한 문자열 |
| symbol.toString()         | 설명을 포함한 문자열 |
| symbol + ''               | TypeError            |
| 템플릿 리터럴의 심벌 보간 | TypeError            |
| Number(symbol), +symbol   | TypeError            |
| Boolean(symbol), !!symbol | true                 |

```js
const symbol = Symbol("token");

console.log(String(symbol)); // 'Symbol(token)'
console.log(symbol.toString()); // 'Symbol(token)'
console.log(Boolean(symbol)); // true
console.log(!!symbol); // true

const conversions = [
  () => symbol + "",
  () => `${symbol}`,
  () => Number(symbol),
  () => +symbol,
];

for (const convert of conversions) {
  try {
    convert();
  } catch (error) {
    console.log(error.name); // 각각 TypeError
  }
}

if (symbol) {
  console.log("심벌 값은 truthy다");
}
```

**명시적 문자열 변환은 가능, 명시적 숫자 변환도 실패.** 모든 심벌 원시값은 truthy다

### 33.2.2 Symbol.for / Symbol.keyFor

#### Symbol.for

전역 심벌 레지스트리에서 문자열 키에 대응하는 심벌을 찾음

- 키가 있으면 기존 심벌을 반환
- 키가 없으면 심벌을 생성하고 등록한 뒤 반환

```js
const first = Symbol.for("study.userId");
const second = Symbol.for("study.userId");

console.log(first === second); // true
console.log(first === local); // false
console.log(first.description); // 'study.userId'
```

```mermaid
flowchart TD
  A["심벌 생성 또는 조회"] --> B{"공유가 필요한가?"}
  B -->|"아니요"| C["Symbol: 새 심벌 생성:]
  B -->|"예"| D{"레지스트리에 키가 있는지?"}
  D -->|"예"| E["기존 심벌 반환"]
  D -->|"아니요"| F["새 심벌 생성·등록 후 반환"]
```

전역 레지스트리는 런타임에 심벌을 공유하기 위한 개념. 브라우저·서버·서로 다른 프로세스가 심벌을 자동 공유하는 분산 저장소는 아님

독립 모듈 간 공유가 필요하면 `Symbol.for()`를 사용할 수 있음. 단순히 한 모듈의 심벌을 export하고 다른 모듈에서 import하는 방법도 있음

#### Symbol.keyFor

등록된 심벌에 대응하는 레지스트리 키를 반환. 등록되지 않은 심벌이면 `undefined`

```js
const shared = Symbol.for("study.uesrId");
const local = Symbol("study.userId");

console.log(Symbol.keyFor(shared)); // 'study.userId'
console.log(Symbol.keyFor(local)); // undefined
console.log(Symbol.keyFor(Symbol.iterator)); // undefined

try {
  Symbol.keyFor("study.userId");
} catch (error) {
  console.log(error.name); // TypeError
}
```

| 구분                  | Symbol('id')        | Symbol.for('id')          |
| --------------------- | ------------------- | ------------------------- |
| 같은 인수로 반복 호출 | 매번 다른 심벌      | 같은 레지스트리 같은 심벌 |
| 인수 역할             | 설명                | 레지스트리 키이자 설명    |
| Symbol.keyfor 결과    | undefined           | 'id'                      |
| 적합한 용도           | 고유한 내부 키·상수 | 의도적으로 공유한 키      |

`description`과 레지스트리 키는 다름. 설명이 있다는 이유만으로 `Symbol.keyFor()`가 값을 반환하지 않음.
