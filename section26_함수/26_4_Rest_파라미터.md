# 26.4 Rest 파라미터

## 26.4.1 기본 문법

Rest 파라미터는 매개변수 앞에 `...`를 붙여 **아직 할당되지 않은 나머지 인수를 배열로 수집**하는 문법

```js
function collect(first, second, ...rest) {
  console.log(first); // 1
  console.log(second); // 2
  console.log(rest); // [3, 4, 5]
  console.log(Array.isArray(rest)); // true
}

collect(1, 2, 3, 4, 5);
```

| 규칙             | 설명                                     |
| ---------------- | ---------------------------------------- |
| 마지막 매개변수  | Rest 뒤에 다른 매개변수를 선언할 수 없음 |
| 하나만 선언      | 남은 인수를 하나의 배열에 수집           |
| 기본값 지정 불가 | `...rest = []`는 문법 오류               |
| 뒤에 쉼표 불가   | `(...rest,)`는 문법 오류                 |
| 인수가 없으면    | 빈 배열 `[]`                             |
| 함수 `length`    | Rest 파라미터는 계산에서 제외            |

```text
function wrong1(...rest, x) {} // SyntaxError
function wrong2(...a, ...b) {} // SyntaxError
function wrong3(...rest = []) {} // SyntaxError
function wrong4(...rest,) {} // SyntaxError
```

```js
function foo(...rest) {}
function bar(x, ...rest) {}
function baz(x, y, ...rest) {}

console.log(foo.length); // 0
console.log(bar.length); // 1
console.log(baz.length); // 2
```

## 26.4.2 Rest 파라미터와 `arguments` 객체

| 구분              | Rest 파라미터                        | `arguments`                        |
| ----------------- | ------------------------------------ | ---------------------------------- |
| 자료 구조         | 실제 배열                            | 유사 배열 객체                     |
| 담기는 인수       | 앞의 매개변수에 배정되지 않은 나머지 | 호출 시 전달한 모든 인수           |
| `map`, `reduce`   | 바로 사용 가능                       | 배열로 변환하거나 메서드 빌려 쓰기 |
| 이터러블          | 예                                   | 현대 JavaScript에서는 예           |
| 일반 함수, 메서드 | 사용 가능                            | 자체 객체 생성                     |
| 화살표 함수       | 자신의 인수 수집 가능                | 자체 객체 없음, 바깥 참조만 가능   |

```js
function compare(first, ...rest) {
  console.log(first); // 1
  console.log(rest); // [2, 3]
  console.log([...arguments]); // [1, 2, 3]
  console.log(Array.isArray(arguments)); // false
  console.log(typeof arguments.map); // undefined
}

compare(1, 2, 3);
```

`arguments`를 배열로 변환하는 방법에 `Array.from(arguments)`, `[...arguments]`, 이전 방식인 `Array.prototype.slice.call(arguments)`가 있다.

```js
function oldSum() {
  const args = Array.prototype.slice.call(arguments);
  return args.reduce((total, value) => total + value, 0);
}

function sum(...args) {
  return args.reduce((total, value) => total + value, 0);
}

const arrowSum = (...args) => args.reduce((total, value) => total + value, 0);

console.log(oldSum(1, 2, 3, 4, 5)); // 15
console.log(sum(1, 2, 3, 4, 5)); // 15
console.log(arrowSum(1, 2, 3, 4, 5)); // 15
console.log(sum()); // 0
```

초기값 `0`을 지정하면 빈 배열에 대해서 `reduce`가 안전하게 실행. 숫자 인수를 전달하는 전제.

## 26.4.3 Rest와 Spread 구분

둘 다 `...`를 사용하나 위치와 역할이 다름

| 문법          | 위치                   | 역할                        | 예시                              |
| ------------- | ---------------------- | --------------------------- | --------------------------------- |
| Rest 파라미터 | 함수 정의의 매개변수   | 여러 인수를 배열로 모음     | `function sum(...args) {}`        |
| Spread        | 함수 호출, 배열 리터럴 | 이터러블을 개별 값으로 펼침 | `sum(...numbers)`, `[...numbers]` |

```js
function sum(...args) {
  return args.reduce((total, value) => total + value, 0);
}

const numbers = [1, 2, 3];
console.log(sum(...numbers)); // 6
```

객체 리터럴의 Spread도 있지만, 이터러블을 펼치는 배열 및 함수 호출의 Spread와는 별도로 이해한다.
