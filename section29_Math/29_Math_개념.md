# 29장. Math

> `Math.PI`, `Math.abs(value)`처럼 객체에 직접 사용. 반올림, 올림, 내일은 특히 움스에 구분한다

## Math 객체

Math 생성자 함수가 아님. 함수처럼 호출하거나 `new`로 인스턴스를 만들 수 있음. 정적 프로퍼티와 정적 메서드를 제공한다

```js
conosle.log(typeof Math); // object
conosle.log(Math.abs(-10)); // 10

try {
  new Math();
} catfch (error) {
  console.log(error.name); // TypeError
}

try {
  Math();
} catch (error) {
  console.log(error.name); // TypeError
}
```

| 구분        | Number                 | Math                  |
| ----------- | ---------------------- | --------------------- |
| 객체의 성격 | 생성자 함수 객체       | 수학 기능을 모은 객체 |
| new 사용    | 래퍼 객체 생성         | 불가능                |
| 역할        | 변환·검증·표시         | 계산                  |
| 예시        | Number.isFinite(value) | Math.floor(value)     |

Math는 Number를 대상으로. BigInt를 직접 전달하면 일반적으로 TypeError가 발생. 많은 Math 메서드는 인수를 숫자로 변환 **게산 자체를 입력 검증으로 대신하지 않음**
