# 28.2 Number 프로퍼티

다음 값은 모두 `Number` 생성자 함수 객체의 정적 프로퍼티다.

| 프로퍼티                 | 의미                                   |
| ------------------------ | -------------------------------------- |
| Number.EPSILON           | `2 ** -52`, 약 `2.220446049250313e-16` |
| Number.MAX_VALUE         | 가장 큰 양의 유한수                    |
| Number.MIN_VALUE         | 가장 작은 양수, 5e-324                 |
| Number.MAX_SAFE_INTEGER  | `2 ** 53 - 1`, `9007199254740991`      |
| Number.MIN_SAFE_INTEGER  | `-(2 ** 53 - 1)`, `-9007199254740991`  |
| Number.POSITIVE_INFINITY | Infinity                               |
| Number.NEGATIVE_INFINITY | -Infinity                              |
| Number.NaN               | NaN                                    |

## 28.2.1 Number.EPSILON

`EPSILON`은 **1과 1보다 큰 가장 가까운 표현 가능한 숫자 사이의 간격**이다. 가장 작은 양수와는 다른 개념이다.

```js
console.log(Number.EPSILON); // 2.220446049250313e-16
console.log(Number.EPSILON === 2 ** -52); // true
console.log(0.1 + 0.2); // 0.30000000000000004
console.log(0.1 + 0.2 === 0.3); // false
```

0.1 같은 일부 십진 소수는 이진수로 정확하게 표현할 수 없어 가까운 값으로 저장된다. 모든 소수가 부정확한 것은 아님. 0.5는 이진수로 표현할 수 있다

### 간단한 오차 비교, 한계

```js
function equalNearOne(a, b) {
  return Math.abs(a - b) < Number.EPSILON;
}

console.log(equalNearOne(0.1 + 0.2, 0.3)); // true
console.log(1000.1 + 1000.2); // 2000.3000000000002
console.log(equalNearOne(1000.1 + 1000.2, 2000.3)); // false
```

**EPSILON은 모든 숫자에 적용할 수 있는 고정 허용오차가 아니다.** 값의 크기, 입력 정밀도, 계산 과정에 맞게 허용오차를 정한다.

### 절대, 상대 허용오차를 고려

```js
function nearlyEqual(
  a,
  b,
  { absoluteTolerance = 0, relativeTolerance = Number.EPSILON } = {},
) {
  if (!Number.isFinite(a) || !Number.isFinite(b)) {
    return false;
  }

  const difference = Math.abs(a - b);
  const scale = Math.max(Math.abs(a), Math.abs(b));

  return difference <= Math.max(absoluteTolerance, relativeTolerance * scale);
}

console.log(nearlyEqual(0.1 + 0.2, 0.3)); // true
console.log(nearlyEqual(1000.1 + 1000.2, 2000.3)); // true
console.log(nearlyEqual(0, 1e-10, { absoluteTolerance: 1e-9 })); // true
console.log(nearlyEqual(1, 2)); // false
```

| 개념          | 의미                                            |
| ------------- | ----------------------------------------------- |
| 절대 허용오차 | 두 값 차이의 절대 크기 기준                     |
| 상대 허용오차 | 값의 크기에 비례한 차이 기준                    |
| 근사비교      | 계산 결과를 어느 범위까지 같은 것으로 볼지 결정 |

## 28.2.2 Number.MAX_VALUE

가장 큰 양의 유한수

```js
console.log(Number.MAX_VALUE); // 1.7976931348623157e+308
console.log(Number.MAX_VALUE * 2); // Infinity
console.log(Infinity > Number.MAX_VALUE); // true
console.log(Number.MAX_VALUE + 1 === Number.MAX_VALUE); // true
```

범위를 크게 넘는 연산은 무한대로 오버플로할 수 있다. 매우 큰 값에 `1`을 더하는 작은 변화는 표현 정밀도 때문에 결과에 반영되지 않을 수 있다.

## 28.2.3 Number.MIN_VALUE

가장 작은 **양수**다. 가장 작은 음수가 아님

```js
console.log(Number.MIN_VALUE); // 5e-324
console.log(Number.MIN_VALUE); // 5e-324
console.log(Number.MIN_VALUE); // 5e-324
console.log(Number.MIN_VALUE); // 5e-324
```

매우 작은 값은 반올림으로 `0`이 되는 언더플로가 발생할 수 있다. 음수 방향의 가장 큰 유한 크기는 `-Number.MAX_VALUE`다

## 28.2.4 Number.MAX_SAFE_INTEGER

## 28.2.5 Number.MIN_SAFE_INTEGER

안전한 정수 범위는 `-(2 ** 53 - 1)`부터 `2 ** 53 - 1`까지다. 이 범위의 정수는 정확하게 표현되고 다른 정수와 혼동되지 않는다.

```js
const max = Number.MAX_SAFE_INTEGER;

console.log(max); // 9007199254740991
console.log(Number.MIN_SAFE_INTEGER); // -9007199254740991
console.log(max + 1); // 9007199254740992
console.log(max + 2); // 9007199254740992
console.log(max + 1 === max + 2); // true
```

범위를 벗어난 모든 정수가 표현 불가능한 것은 아님.

## 28.2.6 Number.POSITIVE_INFINITY

## 28.2.7 Number.NEGATIVE_INFINITY

```js
console.log(Number.POSITIVE_INFINITY === Infinity); // true
console.log(Number.NEGATIVE_INFINITY === -Infinity); // true
console.log(1 / 0); // Infinity
console.log(-1 / 0); // -Infinity
```

0으로 나눈 모든 결과가 무한대인 것은 아니다. `0 / 0`은 `NaN`이다.

## 28.2.8 Number.NaN

유효한 숫자 결과가 아니라는 것을 나타내는 특수 숫자값이다.

```js
console.log(Number.isNaN(Number.NaN)); // true
console.log(0 / 0); // NaN
console.log(Number("hello")); // NaN
console.log(NaN === NaN); // false
console.log(Number.isNaN(NaN)); // true
console.log(Object.is(NaN, NaN)); // true
```

**NaN 여부는 `value === NaN`으로 검사하지 않는다.** `Number.isNaN`을 사용한다.
