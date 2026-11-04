## 33.1 심벌이란

Symbol은 ES6 도입된 **변경 불가능한 원시 타입의 값**

`Symbol()`을 호출할 때 새로운 심벌을 만듬. 설명 문자열이 같아도 서로 다른 값.

```js
const first = Symbol("id");
const second = Symbol("id");

console.log(typeof first); // symbol
console.log(first === second); // false
console.log(first === first); // true
```

주로 다른 코드와 이름이 충돌하지 않은 프로퍼티 키나, 서로 구별되는 상수값에 사용

### JavaScript의 데이터 타입

Symbol이 "7번째 타입". 현재는 BigInt를 포함하여 **원시 타입 7개와 객체 타입 1개**, 8개

| 분류      | 타입                                                     |
| --------- | -------------------------------------------------------- |
| 원시 타입 | undefined, null, boolean, number, bigint, string, symbol |
| 객체 타입 | object                                                   |

객체의 프로퍼티 키는 **문자열 또는 심벌**이다. 숫자를 키로 사용하면 문자열로 변환된다
