## 35.2 배열 리터럴 내부

### 35.2.1 concat

```js
const first = [1, 2];
const second = [3, 4];

console.log(first.concat(second)); // [1, 2, 3, 4]
console.log([...first, ...second]); // [1, 2, 3, 4]
console.log([0, ...first, 9]); // [0, 1, 2, 9]
console.log(first); // [1, 2]
```

새 배열을 만들며 원본 배열을 변경하지 않음. Spread는 깊이가 있는 배열을 재귀적으로 평탄화하지 않음

```js
console.log([...[1, [2, 3]]]); // [1, [2, 3]]
```

### 35.2.2 splice

```js
const nested = [1, 4];
nested.splice(1, 0, [2, 3]);
console.log(nested); // [1, [2, 3], 4]

const legacy = [1, 4];
Array.prototype.splice.apply(legacy, [1, 0].concat([2, 3]));
console.log(legacy); // [1, 2, 3, 4]

const numbers = [1, 4];
const inserted = [2, 3];
const removed = numbers.splice(1, 0, ...inserted);

console.log(numbers); // [1, 2, 3, 4]
console.log(removed); // []
```

splice는 원본을 변경. `splice(start, deleteCount, ...items)` 두 번째 인수는 남길 개수가 아닌 삭제할 개수다

### 35.2.3 배열복사

```js
const original = [1, 2];
const sliced = original.slice();
const spread = [...original];

console.log(sliced); // [1, 2]
console.log(spread); // [1, 2]
console.log(sliced === original); // false
console.log(spread === original); // false
```

둘 다 얕은 복사다. 새 바깥 배열을 만들지만 내부 객체 참조는 공유

```js
const original = [{ score: 10 }];
const copy = [...original];
copy[0].score = 20;

console.log(copy === original); // false
console.log(copy[0] === original[0]); // true
console.log(original[0].socre); // 20
```

희소 배열 빈 슬롯 처리에 차이가 있음

```js
const sparse = [, 1];
const sliced = sparse.slice();
const spread = [...sparse];

console.log(Object.hasOwn(sliced, 0)); // false
console.log(Object.hasOwn(spread, 0)); // true
console.log(spread[0]); // undefined
```

### 35.2.4 이터러블을 배열로 변환

```js
function sumLegacy() {
  const args = Array.prototype.slice.call(arguments);
  return args.reduce((total, value) => total + value, 0);
}

function sumSpread() {
  return [...arguments].reduce((total, value) => total + value, 0);
}

const sumRest = (...args) => args.reduce((total, value) => total + value, 0);

console.log(sumLegacy(1, 2, 3)); // 6
console.log(sumSpread(1, 2, 3)); // 6
console.log(sumRest(1, 2, 3)); // 6
```

arguments는 이터러블이며 유사 배열이다. 새 함수에 인수를 배열로 받는 Rest 파라미터가 간단. 화살표 함수에 자체 arguments가 없음

```js
const arrayLike = { 0: 1, 1: 2, 2: 3, length: 3 };

console.log(Array.prototype.slice.call(arrayLike)); // [1, 2, 3]
console.log(Array.from(arrayLike)); // [1, 2, 3]
console.log(Array.from(new Set([1, 2]))); // [1, 2]

try {
  console.log([...arrayLike]);
} catch (error) {
  console.log(error.name); // TypeError
}
```

slice.call은 length와 인덱스를 읽는 방식이고 임의의 이터러블을 순회하는 만능 변환은 아님
