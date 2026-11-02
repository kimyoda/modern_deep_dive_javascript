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

## 질문 및 답변

### Mth에 new를 사용할 수 없는 이유

- Math는 생성자 함수가 아닌 수학 기능을 제공하는 객체

### Math.PI를 어디에 사용하는 지

- 원의 둘레와 넓이 등 원주율이 필요한 계산에 사용. Number로 표현한 근사값

### Math.abs(null)rhk Math.abs(undefined)의 결과

- 각각 0과 NaN이다. 숫자 변호나 과정이 적용

### Math.round(-1.5)는

- -1이다. 중간값에서 더 큰 정수인 +Infinity 방향을 선택

### Math.ceil(-1.4)와 Math.floor(-1.4)는ㄷ

- 각각 -1과 -2다

### 음수에서 소수 부분만 제거

Math.trunc를 사용.

### Math.sqrt(-9)는 무엇을 반환

- NaN이다. Number의 실수 범위에 음수의 제곱근은 표혐되지 않음

### Math.round 범위

- 0 이상 1 미만. 0은 포함하고 1은 제외

### 1~10 정수 난수를 만드는 기본 식

- Math.floor(Math.random() \* 10) + 1

### 난수를 정수를 만들 때 round 조심

- 양 끝 결과에 대응하는 구간 길이가 다른 결과보다 작아져 분포가 달라질 수 있기 때문

### Math.pow(2, 8)을 지수 연산자로 사용

- 2 ** 8이다. 음수 밑에는 (-2) ** 2처럼 괄호를 사용

### Math.max()와 Math.min() 결과

- 각각 -Infinity, Infinity

### 배열 최댓값, 최솟값

- Math.max(...values), Math.min(...values) 등을 사용. 매우 큰 배열은 reduce나 반복문 고려

### 최댓값 reduce 초기값을 0으로 두면 이슈가 생기는 지

- 모든 값이 음수인 경우 배열엔 없는 0이 최댓값이 될 수 있음. -Infinity를 초기값으로 사용

### Math.round, toFixed의 반환 타입

- Math.roun는 숫자, toFixed는 문자열, 계산값과 표시값을 구분
