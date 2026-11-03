## 32.1 String 생성자 함수

### 원시값과 래퍼 객체

```js
const wrapped = new String("Lee");
const primitive = String(123);

console.log(typeof wrapped); // object
console.log(wrapped.valueOf()); // Lee
console.log(wrapped[0]); // L
console.log(wrapped.length); // 3
console.log(primitive); // '123'
console.log(typeof primitive); // string
console.log(new String().valueOf()); // ''
```

| 표현              | 결과                  |
| ----------------- | --------------------- |
| String(value)     | 문자열 원시값         |
| new String(value) | 문자열을 감싼 객체    |
| String()          | 빈 문자열             |
| new String()      | 빈 문자열을 감싼 객체 |

래퍼의 내부 [[StringData]]를 개발자 도구의 일반 프로퍼티처럼 직접 다루지 않고 일반적인 문자열 저장·비교에 원시값을 사용

```js
console.log(String(1)); // '1'
console.log(String(NaN)); // 'NaN'
console.log(String(Infinity)); // 'Infinity'
console.log(String(true)); // 'true'
console.log(String(false)); // 'false'
console.log(String(null)); // 'null'
console.log(String(undefined)); // 'undefined'
```

### 불변성과 래퍼의 읽기 전용 인덱스

문자열의 문자 자체는 변경되지 않음.

```js
"use strict";

const text = "Lee";

try {
  text[0] = "K";
} catch (error) {
  console.log(error.name); // TypeError
}
console.log(text); // Lee

const wrapped = new String("Lee");
const descriptor = Object.getOwnPropertyDescritor(wrapped, "0");
console.log(descriptor.writable); // false

wrapped.extra = "memo";
console.log(wrapped.extra); // memo
```

래퍼의 문자 인덱스와 length는 읽기 전용이나 **래퍼 객체 전체가 동결된 것은 아님** 별도 프로퍼티를 추가할 수 있음
