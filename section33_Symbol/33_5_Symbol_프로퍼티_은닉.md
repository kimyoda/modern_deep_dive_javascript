## 33.5 심벌과 프로퍼티 은닉

### 일반 문자열 키 조회에서 제외

```js
const internal = Symbol("internal");
const user = {
  name: "Lee",
  [internal]: 123,
};

const names = [];
for (const key in user) {
  names.push(key);
}

console.log(names); // ['name']
console.log(Object.keys(user)); // ['name']
console.log(Object.getOwnPropertyNames(user)); // ['name']

const symbols = Object.getOwnPropertySymbols(user);

console.log(symbols.length); // 1
console.log(symbols[0] === internal); // true
console.log(user[symbols[0]]); // 123
console.log(Reflect.ownKeys(user).map(String)); // ['name', 'Symbol(internal)']
```

심벌 키는 일부 조회 방식에 빠지지만 `Object.getOwnPropertySymbols()`와 `Reflect.ownKeys()`로 찾을 수 있음

**심벌은 완전한 비공개 속성이나 보안 장치가 아님** 접근 제한이 필요하면 클래스의 `#private` 필드나 외부에 노출하지 않은 클로저 상태 등 목적에 맞는 수단 선택

### 조회·복사 방식

| 방식                           | 문자열 키 | 심벌 키 | 대상                                    |
| ------------------------------ | --------- | ------- | --------------------------------------- |
| for...in                       | 포함      | 제외    | 열거 가능한 자체·상속 프로퍼티          |
| Object.keys / values / entries | 포함      | 제외    | 열거 가능한 자체 프로퍼티               |
| Object.getOwnPropertyNames     | 포함      | 제외    | 열거 가능 여부와 관계없는 자체 프로퍼티 |
| Object.getOwnPropertySymbols   | 제외      | 포함    | 열거 가능 여부와 관게없는 자체 프로퍼티 |
| Reflect.ownKeys                | 포함      | 포함    | 열거 가능 여부와 관계없는 자체 프로퍼티 |
| Object.assign / 객체 Spread    | 포함      | 포함    | 열거 가능한 자체 프로퍼티 복사          |

심벌 키라는 이유로 `enumerable`이 false가 되는 것은 아님. 객체 리터럴에서 만든 심벌 프로퍼티는 일반적으로 `enumerable: true`

```js
const spreadCopy = { ...source };
const assignedCopy = Object.assign({}, source);

console.log(Object.getOwnPropertyDescriptor(source, key).enumerable); // true
console.log(spreadCopy[key]); // '내부 메모'
console.log(assignedCopy[key]); // '내부 메모'
```

### JSON 변환

기본 `JSON.stringify()`는 심벌 키 프로퍼티를 제외. 심벌 값도 JSON 데이터로 직접 표현되지 않음

```js
const key = Symbol("internal");

console.log(JSON.stringify({ name: "Lee", [key]: 123 })); // {"name":"Lee"}
console.log(JSON.stringify({ status: Symbol("ready") })); // '{}'
console.log(JSON.stringify([Symbol("ready")])); // ['null']
console.log(JSON.stringify(Symbol("ready"))); // undefined: JSON 문자열이 아님
```

| 위치                  | JSON.stringify 결과 |
| --------------------- | ------------------- |
| 심벌 키 프로퍼티      | 제외                |
| 객체 프로퍼티 심벌 값 | 해당 프로퍼티 제외  |
| 배열 요소 심벌 값     | null                |
| 최상위 심벌 값        | undefined 반환      |

API로 전달할 값은 심벌 설명을 자동 추출하는 대신 명시적인 문자열 코드 등으로 매핑
