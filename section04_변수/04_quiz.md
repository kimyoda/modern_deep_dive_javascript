## section04 변수

### 1. 둘째 줄에서 `total`은 무슨 역할 인 지

```js
var total = 7 + 5;
console.log(total);
```

- A. `7 + 5`를 다시 계산
- B. 변수에 저장된 값 `12`를 참조
- C. 변수의 값을 `undefined`로 초기화
- D. 변수 이름을 문자열 `"total"`로 바꾼다

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `7 + 5`는 첫 줄의 할당을 수행할 때 평가
- **B**: `total`은 변수의 이름, 둘째 줄에서 이름을 통해 저장된 값 `12`을 읽는다.
- **C**: `undefined`를 사용한 초기화는 `var` 선언을 처리할 때 이루어진다. 현재 값을 읽는다.
- **D**: `cosnole.log("total")`처럼 따옴표로 감싸면 변수 이름과 같은 내용의 문자열을 출력할 수 있다

</detail>

### 2. 다읔 코드 출력 순서는?

```js
console.log(score);
var score = 80;
console.log(score);
```

- A. `80` -> `80`
- B. 첫 줄에 `ReferenceError`
- C. `undefined` -> `80`
- D. 변수 이름을 문자열 `"total"`로 바꾼다

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `7 + 5`는 첫 줄의 할당을 수행할 때 평가
- **B**: `total`은 변수의 이름, 둘째 줄에서 이름을 통해 저장된 값 `12`을 읽는다.
- **C**: `undefined`를 사용한 초기화는 `var` 선언을 처리할 때 이루어진다. 현재 값을 읽는다.
- **D**: `cosnole.log("total")`처럼 따옴표로 감싸면 변수 이름과 같은 내용의 문자열을 출력할 수 있다

</detail>
