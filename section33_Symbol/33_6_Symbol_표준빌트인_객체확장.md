## 33.6 심벌과 표준 빌트인 객체 확장

### 문자열 이름으로 확장할 때 문제

다음과 같이 표준 프로토타입에 사용자 메서드를 직접 추가하는 방식은 권장하지 않음

```js
// 권장하지 않는 방식의 형태만 확인
// Array.prototype.sum = function () {
//   return this.reduce((total, value) => total + value, 0);
// };
```

일반 대입으로 ㅊ추가한 문자열 키 메서드는 열거 가능한 상속 프로퍼티가 되는 문제도 있음

### 심벌 키를 사용

새 `Symbol()`을 키로 쓰면 다른 코드가 독립적으로 생성한 키와 이름이 겹치지 않음

```js
const sumKey = Symbol("sum");

Object.defineProperty(Array.prototype, sumKey, {
  configurable: true,
  value: function () {
    return this.reduce((total, value) => total + value, 0);
  },
});

try {
  console.log([1, 2][sumKey]()); // 3
  console.log([1, 2, 3][sumKey]()); // 6
} finally {
  delete Array.prototype[sumKey];
}
```

**키 충돌 문제**를 줄임. 전역 프로토타입 변경이 모든 배열에 영향을 주는걸 해결하지 않음

`Symbol.for('sum')`을 쓰면 같은 레지스트리 키를 사용하는 코드가 동일한 프로퍼티에 접근하여 충돌 가능성이 남음

```js
function sum(numbers) {
  return numbers.reduce((total, value) => total + value, 0);
}

console.log(sum([1, 2, 3])); // 6
console.log(sum([])); // 0
``;
```
