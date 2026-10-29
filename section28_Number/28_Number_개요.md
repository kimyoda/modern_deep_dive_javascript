# 28장 Number

> 숫자 변환, 숫자 검증, 숫자 표시를 구분한다. `Number(value)`는 변환, `Number.isFinite` 등의 정적 메서드는 검증, `toFixed` 등의 프로토타입 메서드는 문자열 표시를 담당한다.

## Number 타입

JavaScript의 `Number`는 **IEEE 754의 64비트 배정밀도 이진 부동소수점**을 사용한다. `10`과 `10.5`는 모두 같은 `number` 타입이다. 큰 정수를 다루는 `bigint`는 별도의 타입이다.

```js
console.log(typeof 10); // number
console.log(typeof 10.5); // number
console.log(typeof NaN); // number
console.log(typeof Infinity); // number
console.log(typeof 10n); // bigint
```

| 값                  | 의미                                    |
| ------------------- | --------------------------------------- |
| 유한수              | 보통의 숫자 계산 결과                   |
| Infinity, -Infinity | 양, 음의 무한대                         |
| NaN                 | 유효한 숫자 결과가 아니라는 특수 숫자값 |

`NaN`은 이름이 Not-a-Number여도 타입은 `number`다. `typeof value === 'number'`만으로 정상적인 유한수인지 확인할 수 없다.
