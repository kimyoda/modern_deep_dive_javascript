# 37장. Set과 Map

> **Set은 중복 없는 값의 집합, Map은 다양한 타입의 키를 사용하는 키·값 컬렉션. 둘 다 삽입 순서로 순회**

## 먼저 비교

| 구분          | Array                     | Set          | 일반 객체                       | Map                |
| ------------- | ------------------------- | ------------ | ------------------------------- | ------------------ |
| 저장 단위     | 인덱스와 요소             | 중복 없는 값 | 프로퍼티 키와 값                | 키와 값            |
| 키            | 배열 인덱스 등            | 별도 키 없음 | 문자열·심벌                     | 모든 JavaScript 값 |
| 개수          | length: 빈 슬롯 포함 길이 | size         | 세는 범위에 따라 조회 방식 선택 | 냨ㄷ               |
| 기본 이터러블 | O                         | O            | X                               | O                  |
| 기본 순회 값  | 요소값                    | 요소값       | 별도 구현 필요                  | `[key, value]`     |

Set과 Map의 삽입 순서에 따른 순회는 **ECMAScript 표준에 규정된 동작**이다

## 37.1 Set

### 37.1.1 Set 객체 생성

```js
const empty = new Set();
const numbers = new Set([1, 2, 3, 3]);
const letters = new Set("hello");

console.log(empty.size); // 0
console.log([...numbers]); // [1, 2, 3]
console.log([...letters]); // ['h', 'e', 'l', 'l', 'o']

const unique = (values) => [...new Set(values)];
console.log(unique([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]
```

생성자는 이터러블을 받고 중복을 제거. 배열의 최초 등장 순서는 유지. 일반적으로 new 없이 Set을 호출할 수 없음.

indexOf 기반 중복 제거는 NaN을 찾지 못하지만 Set은 NaN도 중복 제거. 객체 배열의 내용까지 비교해 제거하는 것은 아님

### 37.1.2 요소 개수

```js
"use strict";

const set = new Set([1, 2, 3]);
const { size } = set;
console.log(size); // 3
console.log(Object.getOwnPropertyDescriptor(Set.prototype, "size").set); // undefined

try {
  set.size = 10;
} catch (error) {
  console.log(error.name); // TypeError
}

set.add(4);
console.log(size, set.size); // 3 4
```

size는 setter 없는 접근자다. 구조 분해한 size는 읽은 당시의 숫자이고 Set과 자동으로 연결되지 않음

### 37.1.3 요소 추가

```js
const set = new Set();
console.log(set.add(1) === set); // true
set.add(2).add(2).add(3);
console.log([...set]); // [1, 2, 3]

const mixed = new Set([1, "1", true, undefined, null, {}, [], () => {}]);
console.log(mixed.size); // 8
```

add는 원본 Set을 변경 **같은 Set 객체**를 반환하여 체이닝할 수 있음. 중복 추가는 오류가 아니고 기존 순서를 바꾸지 않음

#### 값의 동등성: SameValueZero

```js
console.log(NaN === NaN); // false
console.log(0 === -0); // true

const set = new Set([NaN, NaN, 0, -0, 1, "1"]);
console.log(set.size); // 4
console.log(set.has(NaN)); // true

const user = { id: 1 };
const users = new Set([user, user, { id: 1 }]);
console.log(users.size); // 2
console.log(users.has({ id: 1 })); // false
console.log(users.has(user)); // true
```

| 비교                  | Set·Map 취급 |
| --------------------- | ------------ |
| NaN, NaN              | 같음         |
| +0과 -0               | 같음         |
| 1과 '1'               | 다름         |
| 같은 객체 참조        | 같음         |
| 내용만 같은 별도 객체 | 다름         |

### 37.1.4 존재 확인 / 37.1.5 삭제 / 37.1.6 일괄 삭제

```js
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(9)); // false
console.log(set.delete(2)); // true
console.log(set.delete(9)); // false
console.log([...set]); // [1, 3]

console.log(set.clear()); // undefined
console.log(set.siz); // 0
```

delete에 인덱스가 아니 삭제할 **값**을 전달. 반환값이 불리언이므로 `set.delete(1).delete(2)`처럼 체이닝할 수 없음

| API           | 반환값         | 원본 변경 |
| ------------- | -------------- | --------- |
| add(value)    | 같은 Set       | O         |
| has(value)    | 불리언         | X         |
| delete(value) | 삭제 성공 여부 | O         |
| clear()       | undefined      | O         |

### 37.1.7 요소 순회

```js
const set = new Set([3, 1, 2]);
const rows = [];
const result = set.forEach((value, sameValue, original) => {
  rows.push([value, sameValue, original === set]);
});

console.log(rows); // [[3, 3, true], [1, 1, true], [2, 2, true]]
console.log(result); // undefined
console.log([...set]); // [3, 1, 2]

const [first, ...rest] = set;
console.log(first, rest); // 3 [1, 2]

const visited = [];
for (const value of set) {
  visited.push(value);
}
console.log(visited); // [3, 1, 2]

console.log([...set.keys()]); // [3, 1, 2]
console.log([...set.values()]); // [3, 1, 2]
console.log([...set.entries()]); // [[3, 3], [1,1], [2, 2]]
```

Set.forEach의 두 번째 인수도 값. 배열 인덱스가 아님. keys와 values는 같은 값을 순회, entries는 `[value, value]`를 제공

```js
const set = new Set([3, 1, 2]);
set.add(1);
console.log([...set]); // [3, 1, 2]

set.delete(1);
set.add(1);
console.log([...set]); // [3, 2, 1]
```

삭제 후 다시 넣은 값은 끝에 추가

### 37.1.8 집합 연산

| 연산            | 의미                         |
| --------------- | ---------------------------- |
| 교집합 A ∩ B    | 두 집합에 모두 있는 값       |
| 합집합 A ∪ B    | 어느 한쪽에라도 있는 값      |
| 차집합 A − B    | A에 있고 B에는 없는 값       |
| 부분집합 A ⊆ B  | A의 모든 값이 B에 있음       |
| 상위 집합 A ⊇ B | B의 모든 값이 A에 있음       |
| 여집합 U − A    | 전체집합 U에서 A를 제외한 값 |

#### 직접 구현

Set.prototype을 덮어쓰지 않고 별도 함수로 구현

```js
function insertsection(a, b) {
  const result = new Set();
  for (const value of a) {
    if (b.has(value)) {
      result.add(value);
    }
  }
  return result;
}

function union(a, b) {
  const result = new Set(a);
  for (const value of b) {
    result.add(value);
  }
  return result;
}

function difference(a, b) {
  const result = new Set(a);
  for (const value of b) {
    result.delete(value);
  }
  return result;
}

function isSuperset(a, b) {
  for (const value of b) {
    if (!a.has(value)) {
      return false;
    }
  }
  return true;
}

const a = new Set([1, 2, 3, 4]);
const b = new Set([2, 4]);

console.log([...intersection(a, b)]); // [2, 4]
console.log([...union(a, b)]); // [1, 2, 3, 4]
console.log([...difference(a, b)]); // [1, 3]
console.log([...difference(a, b)]); // []
console.log(isSuperset(a, b)); // true
console.log(isSuperset(a, b)); // false
```

배열 변환·고차 함수로 표현하는 방법

```js
const intersection = (a, b) => new Set([...a].filter(value) => b.has(value));
const union = (a, b) => new Set([...a, ...b]);
const difference = (a, b) => new Set([...a].filter(value =. !b.has(value)));
const isSuperset = (a, b) => [...b].every(value => a.has(value));

const a = new Set([1, 2, 3, 4]);
const b = new Set([2, 4]);

console.log([...intersection(a, b)]); // [2, 4]
console.log([...union(a, b)]); // [1, 2, 3, 4]
console.log([...difference(a, b)]); // [1, 3]
console.log(isSuperset(a, b)); // true
```

상위 집합 검사에 매번 배열 includes를 사용하기보다 Set.has를 그대로 이용할 수 있음. 위 함수는 결과 순회 순서를 첫 번째 입력 기준으로 구현 함수

#### 현대 표준 집합 메서드

| 메서드                     | 반환값                  |
| -------------------------- | ----------------------- |
| intersection(other)        | 교집합 Set              |
| union(other)               | 합집합 Set              |
| difference(other)          | 차집합 Set              |
| symmetricDifference(other) | 한쪽에만 있는 값의 Set  |
| isSubestOf(other)          | 부분집합 여부           |
| isSupersetOf(other)        | 상위 집합 여부          |
| isDisjointFrom(other)      | 공통 요소가 없는지 여부 |

```js
const a = new Set([1, 2, 3, 4]);
const b = new Set([2, 4]);

if (typeof a.union === "function") {
  console.log([...a.intersection(b)]); // [2, 4]
  console.log([...a.union(b)]); // [1, 2, 3, 4]
  console.log([...a.difference(b)]); // [2, 4]
  console.log([...a.symmetricDifference(b)]); // [1, 3]
  console.log(b.isSubestOf(a)); // true
  console.log(a.isSupersetOf(b)); // true
  console.log(a.isDisjoinFrom(b)); // true
}
```

직접 구현한 isSuperset과 표준 메서드 isSupersetOf의 이름을 구분. 표준 intersection은 두집합의 크기 등에 따라 작은 쪽의 순서로 순회할 수 있어, 모든 연산 결과가 항상 왼쪽 집합 순서라고 가정하지 않음
