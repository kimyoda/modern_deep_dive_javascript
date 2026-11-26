## section09 타입 변환과 단축 평가

### 1. 다음 두 표현식은 어떤 값으로 평가되는 지

```js
console.log("3" + 2, "3" - 2);
```

- A. `5`, `1`
- B. `'32'` -> `'1'`
- C. `'32'` -> `1`
- D. `5` -> `'1'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: `+`는 한쪽이 문자열이면 덧셈이 아닌 연결로 동작
- **B**: `-`는 `'3'`을 숫자로 변환해 계산
- **C**: 첫 표현식은 문자열 연결, 둘째는 변환 후 숫자 뺄셈
- **D**: 첫 결과는 문자열, 둘째 결과는 숫자

</detail>

### 2. 조건에 들어갈 문자열 `'0'`은 어떻게 처리되는 지?

```js
if ("0") {
  console.log("실행");
} else {
  console.log("건너뜀");
}
```

- A. `'실행'`이 출력
- B. `'건너뜀'`이 출력
- C. 두 문구가 모두 출력
- D. `TypeError`가 발생

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: `'0'`은 비어 있지 않은 문자열이라 참으로 판단
- **B**: 숫자 `0`은 거짓으로 판단하나 여기서는 문자열
- **C**: `if...else`에서 두 분기 중 한 곳만 실행
- **D**: 조건 자리에 문자열을 넣어도 불리언으로 판단

</detail>

### 3. `String()`을 실행한 뒤 두 값의 타입은 무엇인지?

```js
const count = 12;
const label = String(count);

console.log(typeof count, typeof label);
```

- A. `'string'`, `'string'`
- B. `'number'`, `'number'`
- C. `'string'`, `'number'`
- D. `'number'`, `'string'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: `String(count)`는 `count`에 새 값을 할당하지 않음
- **B**: 변환 결과인 `label`에 문자열 `'12'`가 들어 있음
- **C**: 원본은 숫자이고 함수의 반환값은 문자열
- **D**: `count`는 숫자로 남고, 변환한 값만 `label`에 저장

</detail>

### 4. 다음 `Number()` 호출 결과는

```js
console.log(Number(" "), Number("12px"));
```

- A. `0`, `12`
- B. `0`, `NaN`
- C. `NaN`, `12`
- D. `'NaN`, `NaN`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `Number()`는 앞부분의 숫자만 따로 읽지 않음
- **B**: 공백만 있는 문자열은 `0`, 전체를 숫자로 해석할 수 없는 `'12px'`는 `NaN`이 된다
- **C**: 첫 값은 `0`이고 둘째 값은 `NaN`이다
- **D**: 공백만 있다고 항상 `NaN`이 되는 것은 아님

</detail>

### 5. `parseInt()`와 `Number()`는 각가 무엇을 반환하는 지

```js
console.log(parseInt("18px", 10), Number("18px"));
```

- A. `18`, `18`
- B. `NaN`, `18`
- C. `18`, `NaN`
- D. `NaN`, `NaN`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: `Number()`는 `px`가 붙은 문자열 전체를 숫자로 해석하지 못함
- **B**: `parseInt()`는 앞의 숫자 `18`을 읽을 수 있음
- **C**: `parseInt()`는 10진 숫자로 읽다가 `p`에서 멈춘다. `Number()`는 전체 변환에 실패
- **D**: 두 함수 모두 실패하는 것은 아님. 첫 함수는 `18`을 반환

</detail>

### 6. `Boolean()`으로 변환한 결과

```js
console.log(Boolean(""), Boolean([]), Boolean(NaN));
```

- A. `false`, `true`, `false`
- B. `false`, `false`, `false`
- C. `true`, `true`, `false`
- D. `false`, `true`, `true`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: 빈 문자열과 `NaN`은 거짓, 빈 배열은 객체라 참으로 변환
- **B**: 배열은 비어있어도 불리언으로 변환하면 `true`
- **C**: 빈 문자열은 `''`은 `false`로 변환
- **D**: `NaN`은 `false`로 변환

</detail>

### 7. 프로필 이름으로 어떤값이 출력인지

```js
const name = "";
const displayName = name || "손님";

console.log(displayName);
```

- A. `''''`
- B. `false`
- C. `true`
- D. `손님`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: 빈 문자열은 거짓으로 판단되어 오른쪽 값을 사용
- **B**: 왼쪽을 거짓으로 판단해도 연산 전체가 `false`를 반환하는 것은 아님
- **C**: 이 `||` 식은 불리언으로 변환된 값 대신 피연산자 값을 반환
- **D**: `name`이 빈 문자열이라 오른쪽 값인 `'손님'`으로 평가

</detail>

### 8. `&&`는 오른쪽 함수를 호출하는지

```js
let calls = 0;

function record() {
  calls += 1;
  return "기록됨";
}

const result = false && record();
console.log(result, calls);
```

- A. `'기록됨'`, `1`
- B. `false`, `0`
- C. `false`, `1`
- D. `undefined`, `0`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: 왼쪽이 `false`라 `record()`를 호출하지 않음
- **B**: `&&`는 왼쪽이 거짓이면 그 값을 반환하고 오른쪽 평가를 건너뜀
- **C**: 오른쪽을 호출하지 않으므로 횟수는 증가하지 않음
- **D**: 이 식은 왼쪽 값 `false`를 그대로 반환

</detail>

### 9. 옵셔널 체이닝을 사용한 다음 코드 결과는?

```js
const profile = null;

console.log(profile?.name);
```

- A. `null`
- B. `'name'`
- C. `undefined`
- D. `TypeError`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: 왼쪽 값은 `null`이나 옵셔널 체이닝 식 결과는 `undefined`
- **B**: `name`은 읽으려는 프로퍼티 이름이지 출력값이 아님
- **C**: `profile`이 `null`이므로 접근을 건너뛰고 `undefined`를 반환
- **D**: `profile.name`이면 오류가 나지만 `?.`가 접근을 막음

</detail>

### 10. `??`, `||`의 결과는 각각 무엇인지

```js
const quantity = 0;

console.log(quantity ?? 1, quantity || 1);
```

- A. `0`, `1`
- B. `1`, `1`
- C. `0`, `0`
- D. `1`, `0`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: `??`는 `0`을 유지, `||`는 `0`을 거짓으로 판단해 기본값을 사용
- **B**: `??`는 왼쪽이 `null` 또는 `undefined`일 때만 오른쪽을 사용
- **C**: `||`에서 `0`이 거짓으로 판단돼 오른쪽 `1`을 사용
- **D**: `0`을 유지하는 쪽은 `||`가 아닌 `??`이다

</detail>
