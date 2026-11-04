# 34장 이터러블

> 이터러블은 순회 방법을 제공, 이터레이터는 순회 위치와 다음 값을 관리

## 34.1 이터레이션 프로토콜

ES6에서 도입된 이터레이션 프로토콜은 다양한 자료구조를 같은 방식으로 순회하기 위한 규약

| 구분                | 기능                                    | 역할                          |
| ------------------- | --------------------------------------- | ----------------------------- |
| 이터러블 프로토콜   | `[Symbol.iterator]()`가 이터레이터 반환 | 순회를 시작하는 방법 제공     |
| 이터레이터 프로토콜 | `next()`가 결과 객체 반환               | 다음 값과 종료 여부 제공      |
| 이터레이터 결과     | `value`, `done`                         | 현재 값과 순회 완료 상태 표현 |

### 34.1.1 이터러블

직접 정의하거나 상속받은 Symbol.iterator 메서드로 이터레이터를 만들 수 있는 값

```js
const hasIteratorMethod = (value) =>
  value != null && typeof value[Symbol.iterator] === "function";

console.log(hasIteratorMethod([])); // true
console.log(hasIteratorMethod("")); // true
console.log(hasIteratorMethod(new Map())); // true
console.log(hasIteratorMethod(new Set())); // true
console.log(hasIteratorMethod({})); // false
console.log(hasIteratorMethod(null)); // false
```

위 함수는 메서드 존재 여부만 확인, 메서드가 올바른 이터레이터를 반환하는지까지 보장하지 않음

```js
const array = [1, 2, 3];
console.log(Symbol.iterator in array); // true
console.log([...array]); // [1, 2, 3]

const [first, ...rest] = array;
console.log(first, rest); // 1 [2, 3]

const object = { a: 1, b: 2 };
console.log({ ...object }); // {a:1, b:2}

try {
  console.log([...object]);
} catch (error) {
  console.log(error.name); // TypeError
}
```

**객체 Spread는 이터러블 순회와 다른 규칙**으로 동작. 일반 객체를 `{ ...object}`로 복사할 수 있다고 해서 `[...object]`나 for...of도 가능한 것은 아님.

### 34.2 이터레이터

```js
const iterator = [1, 2, 3][Symbol.iterator]();

console.log(typeof iterator.next); // function
console.log(iterator.next()); // { value: 1, done: false}
console.log(iterator.next()); // { value: 2, done: false}
console.log(iterator.next()); // { value: 3, done: false}
console.log(iterator.next()); // { value: undefined, done: true}
```

done이 true 결과는 순회 종료를 뜻한다. for...of는 그 결과의 value를 본문에 전달하지 않음. 사용자 정의 결과에 value·done을 생략할 수 있고, 생략한 done은 falsy로 해석
