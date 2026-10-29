# 28.1 Number 생성자 함수

## 1. new Number는 래퍼 객체를 만든다

```js
const zero = new Number();
const ten = new Number(10);
const converted = new Number("10");
const invalid = new Number("hello");

console.log(zero.valueOf()); // 0
console.log(ten.valueOf()); // 10
console.log(converted.valueOf()); // 10
console.log(Number.isNaN(invalid.valueOf())); // true
console.log(typeof ten); // object
console.log(ten instanceof Number); // true
```

래퍼 객체는 내부 `[[NumberData]]` 슬롯에 숫자 값을 가진다. 개발자 도구에 보이는 `[[PrimitiveValue]]` 등의 표시 이름은 환경에 따라 다를 수 있고 일반 프로퍼티처럼 직접 접근하는 값이 아니다

`valueOf()`는 래퍼 안의 숫자 원시값을 반환한다.

## 2. Number를 일반 호출하면 숫자 원시값을 반환

```js
const value = Number("10.53");

console.log(value); // 10.53
console.log(typeof value); // number
console.log(Number()); // 0
console.log(Number(undefined)); // NaN
```

| 코드              | 결과                |
| ----------------- | ------------------- |
| Number(10)        | 숫자 원시값 10      |
| new Number(10)    | 숫자 10을 감싼 객체 |
| Number()          | 숫자 0              |
| new Number()      | 숫자 0을 감싼 객체  |
| Number(undefined) | NaN                 |

**일반적인 값 저장과 계산에서는 숫자 원시값을 사용한다.** 숫자 원시값도 메서드에 접근할 때 래퍼 기능을 이용하여 `(10).toString()`처럼 호출할 수 있다.

## 3. 명시적 숫자 변환

```js
console.log(Number("0")); // 0
console.log(Number("-1")); // -1
console.log(Number("10.53")); // 10.53
console.log(Number(" 10 ")); // 10
console.log(Number("")); // 0
console.log(Number("   ")); // 0
console.log(Number(true)); // 1
console.log(Number(false)); // 0
console.log(Number(null)); // 0
console.log(Number("10px")); // NaN
```

| 입력                   | 변환 결과 |
| ---------------------- | --------- |
| 숫자 문자열            | 해당 숫자 |
| 빈 문자열, 공백 문자열 | 0         |
| true / false           | 1 / 0     |
| null                   | 0         |
| undefined              | NaN       |
| 'hello', '10px'        | NaN       |

빈 입력도 `0`으로 바뀔 수 있으므로 입력을 검증할 때는 먼저 빈 값을 확인한다. 문자열 변환에 실패하면 보통 `NaN`이 되지만, `Symbol`이나 일부 객체를 변환할 때는 예외가 발생할 수 있다.

## 4. 래퍼 객체의 함정

```js
const wrapped = new Number(0);

console.log(Boolean(0)); // false
console.log(Boolean(wrapped)); // true
console.log(wrapped === 0); // false
console.log(wrapped.valueOf() === 0); // true
console.log(Number.isFinite(new Number(10))); // false
```

객체는 내부 숫자가 `0`이어도 truthy다. `Number.isFinite` 등은 숫자 원시값만 검사하며, 래퍼 객체를 숫자로 변환해 검사하지 않는다.
