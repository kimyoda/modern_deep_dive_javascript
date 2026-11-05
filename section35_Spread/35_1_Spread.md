# 35장. 스프레드 문법

> Spread는 펼치고, Rest는 모은다. 배열·인수 Spread와 객체 Spread는 적용 규칙이 다름

## 기본 개념

Spread 문법 `...`는 값을 인수·배열 요소·객체 프로퍼티가 놓이는 자리에 펼침. 단독으로 값을 만드는 연산자는 아님

| 사용             | 예시            | 대상과 규칙                  |
| ---------------- | --------------- | ---------------------------- |
| 함수 호출의 인수 | `fn(...values)` | 이터러블을 인수로 펼침       |
| 배열 리터럴      | `[...values]`   | 이터러블을 요소로 펼침       |
| 객체 리터럴      | `{ ...source}`  | 자체 열거 가능 프로퍼티 복사 |

함수·배열 Spread는 ES2015, 객체 Rest·Spread는 ES2018 표준

```js
console.log(...[1, 2, 3]); // 1 2 3
console.log([..."Hello"]); // ['H', 'e', 'l', 'l', 'o']
console.log([...new Set([1, 1, 2])]); // [1, 2]
console.log([...new Map(["a", 1])]); // [['a', 1]]

try {
  console.log(...{ a: 1, b: 2 });
} catch (error) {
  console.log(error.name); // TypeError
}

// 아래는 문법 오류
// const values = ...[1, 2, 3] // SyntaxError
```

## 35.1 함수 호출문 인수 목록

### Math.max, apply 비교

```js
const numbers = [1, 2, 3];

console.log(Math.max(1)); // 1
console.log(Math.max(1, 2)); // 2
console.log(Math.max(1, 2, 3)); // 3
console.log(Math.max()); // -Infinity
console.log(Math.max(numbers)); // NaN
console.log(Math.max.apply(null, numbers)); // 3
console.log(Math.max(...numbers)); // 3
console.log(Math.max(...[])); // -Infinity
```

Math.max(numbers)는 배열 하나를 인수로 받음. Math.max(...numbers)는 숫자 1, 2, 3를 각각 받음

"배열을 전달하면 항상 NaN"은 정확하지 않음. 숫자 변호나 결과에 따라 Math.max([])는 0, Math.max([3])은 3이다. 배열 요소의 최댓값을 구하려는 의도에 맞지 않음

큰 배열을 한 번에 함수 인수로 펼치면 엔진의 인수 개수 제한에 걸릴 수 있음.

```js
const numbers = [1, 2, 3];
const max = numbers.reduce(
  (result, value) => Math.max(result, value),
  -Infinity,
);
console.log(max); // 3
```

### Spread, Rest 파라미터

```js
function sum(...args) {
  return args.reduce((total, value) => total + value, 0);
}

const numbers = [1, 2, 3];
console.log(sum(...numbers)); // 6
console.log(sum()); // 0
```

| 비교 | Spread                       | Rest 파라미터              |
| ---- | ---------------------------- | -------------------------- |
| 위치 | 함수 호출 인수 등            | 함수 정의 매개변수         |
| 방향 | 하나의 이터러블 -> 여러 인수 | 여러 인수 -> 하나의 배열   |
| 예시 | `sum(...numbers)`            | `function sum(...args) {}` |
