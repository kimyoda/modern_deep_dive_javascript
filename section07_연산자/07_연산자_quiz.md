## section07 연산자

### 1. 다음 코드에서 두 표현식 결과는?

```js
console.log(2 + 3 * 4, (2 + 3) * 4);
```

- A. `20`, `20`
- B. `14`, `14`
- C. `14`, `20`
- D. `20`, `14`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: 첫 번째 표현식에 괄호가 없어 `3 * 4`를 먼저 계산
- **B**: 두 번째 표현식에서 괄호 안 `2 + 3` 먼저 평가
- **C**: 첫 번째는 `2 + 12` 계산, 두 번째 `5 * 4`로 계산
- **D**: `20`은 괄호가 있는 두 번째 결과, `14`는 괄호가 없는 첫 번째 결과

</detail>

### 2. 다음 코드에 `first`, `second`의 값은 각각

```js
const first = +"5" + 2;
const second = 5 + "2";
```

- A. 숫자 `7`, 문자열 `'52'`
- B. 문자열 `'52'`, 숫자 `7`
- C. 숫자 `7`, 숫자 `7`
- D. 문자열 `'52'`, 문자열 `'52'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: 첫 줄은 단항 `+`로 `'5'`를 숫자로 바꾼 뒤 더한다. 둘째 줄은 숫자 `5`를 문자열로 바꿔 `'2'`를 뒤에 붙인다
- **B**: 첫 줄의 문자열은 덧셈 전에 숫자로 변환. 둘째 줄 `'2'`는 문자열 피연산자
- **C**: 둘째 줄 이항 `+`는 숫자 덧셈이 아니라 문자열 연결 동작
- **D**: 첫 줄은 `5 + 2`와 같이 두 숫자의 덧셈으로 계산

</detail>

### 3. 다음 코드 출력 결과는

```js
let value = 3;
const first = value++;
const second = ++value;

console.log(first, second, value);
```

- A. `4 5 5`
- B. `3 4 5`
- C. `3 4 4`
- D. `3 5 5`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: 후위 증가식 `value++`은 증가 전 값 `3`으로 평가. `first`에 `3`이 들어감
- **B**: 둘째 증가식은 전위 증가라 먼저 `value`를 `5`로 바꿈. `second`는 `5`
- **C**: `value++`, `++value` 모두 변수에 저장된 값을 변경. 두 번 증가한 최종 값은 `5`
- **D**: 후위 증가에서 `3`이 `first`에 들어가고 `value`는 4, 전위 증가에서 `5`가 `second`에 들어가고 `value`도 `5`

</detail>

### 4. 다음 코드는 어떤 두 값을 출력할 지

```js
let score = 10;
const result = (score += 5);

console.log(score, result);
```

- A. `10 15`
- B. `15 15`
- C. `15 undefined`
- D. `10 5`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `+=`는 계산뿐 아니라 왼쪽 변수에 값을 저장하여 `score`도 `15`로 바뀜.
- **B**: `score += 5`는 `score`에 `15`를 할당, 표현식 자체도 `15`로 평가
- **C**: 할당 표현식 평가 결과는 할당된 값. `result`에 `15`가 들어감
- **D**: `5`는 더할 값. `score`에 저장되는 것은 더한 결과인 `15`

</detail>

### 5. 다음 코드 출력 순서는

```js
console.log(0 == false, 0 === false, 5 != "5", 5 !== "5");
```

- A. `true true false false`
- B. `false false true true`
- C. `true false false true`
- D. `true false true false`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: `===`와 `!==`는 타입을 변환하지 않음. 두 번째 결과는 `false`고 네 번째 결과는 `true`
- **B**: `0 == false`에 `false`가 `0`으로 변환. `5 != '5'`에서 문자열이 숫자로 변환되어 두 값이 다르지 않음
- **C**: 동등 비교에 `0`과 `false`는 같게 처리., 일치 비교에서는 서로 다름. `5`와 `'5'`도 동등 비교에서 같고 일치 비교에서 다름
- **D**: `!=`는 타입 변환을 수반하는 동등 비교 반대. `!==`는 타입까지 구분하는 일치 비교 반대

</detail>

### 6. 다음 코드 출력 결과는 무엇인지

```js
const score = 70;
const label = score >= 70 ? "통과" : "재시도";

console.log(label);
```

- A. `'통과'`
- B. `'재시도'`
- C. `true`
- D. `undefined`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: `70 >= 70`은 `true`이므로 `?` 뒤쪽 첫 번째 값인 `'통과'`를 선택
- **B**: `'재시도'`는 조건이 거짓일 때 선택. `>=`는 값이 같은 경우도 포함
- **C**: `true`는 비교식 평가 결과. 삼항 조건 연산식 전체는 선택한 분기의 값으로 평가
- **D**: 두 분기 모두 문자열이므로 실행된 분기의 값이 `label`에 저장

</detail>

### 7. 다음 코드에서 `a`, `b`, `c`의 값은 각각 무엇인지

```js
const a = 0 || 10;
const b = "" && "입력";
const c = !"0";
```

- A. `0`, `''`, `true`
- B. `10`, `'입력'`, `false`
- C. `true`, `false`, `false`
- D. `10`, `''`, `false`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: `0`은 거짓으로 취급 `0 || 10`은 `10`을 반환. 문자열 `'0'`은 비어 있지 않아 참으로 취급
- **B**: `''`는 거짓으로 취급. `&&`는 이때 오른쪽을 평가하지 않고 왼쪽 값 `''`를 반환
- **C**: `||`와 `&&`는 여기서 불리언으로 바꾼 값을 반환하지 않음. 선택된 피연산자 값 자체를 반환
- **D**: `a`는 오른쪽 값 `10`, `b`는 왼쪽 값 `''`이다. `!`는 참으로 취급되는 `'0'`을 부정하여 `c`는 `false`

</detail>

### 8. 다음 코드 출력 결과

```js
function greet() {}

console.log(typeof [], typeof greet);
```

- A. `'array'`, `'function'`
- B. `'object'`, `'function'`
- C. `'object'`, `'object'`
- D. `'array'`, `'undefined'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: 배열은 객체, `typeof`는 배열에 대해 `'array'`를 반환하지 않음
- **B**: 배열의 `typeof` 결과는 `'object'`고, 함수 `typeof` 결과는 `'function'`임
- **C**: 함수는 객체의 한 종류지만 `typeof`는 함수에 대해 `'function'`을 반환.
- **D**: `typeof greet`는 함수를 호출한 결과가 아니라 함수 자체 타입을 확인. 배열인지 확인할 때 `Array.isArray()`를 쓸 수 있음

</detail>

### 9. 다음 코드의 출력 결과는?

```js
const todo = { done: false };
const deleted = delete todo.done;

console.log(deleted, "done" in todo, todo.done);
```

- A. `true`, `true`, `false`
- B. `false`, `true`, `false`
- C. `true`, `false`, `undefined`
- D. `true`, `false`, `null`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: `delete`가 성공하면 `done` 프로퍼티는 제거. 이 객체에서 `'done in todo'`가 `false`
- **B**: 이 프로퍼티는 삭제할 수 있음. `const`는 변수의 재할당을 막으나 프로퍼티를 삭제하는 것은 막지않음
- **C**: 삭제가 성공해 `deleted`가 `true`. `done`은 객체나 프로토타입 체인에 없어 존재 확인은 `false`, 값 조회는 `undefined`이다.
- **D**: 삭제된 프로퍼티가 다시 조회될 때 자동으로 `null`이 들어가는 것은 아님. 예제 조회 결과는 `undefined`

</detail>

### 10. 다음 코드 출력 결과는?

```js
console.log(2 ** (3 ** 2));
```

- A. `512`
- B. `64`
- C. `36`
- D. `256`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: 지수 연산자 `**`는 오른쪽부터 결합. `2 ** (3 ** 2)`이므로 `2 ** 9`결과인 `512`
- **B**: `64`는 `(2 ** 3) ** 2`처럼 왼쪽 계산을 괄호로 먼저 묶었을 때 나오는 값
- **C**: `36`는 `(2 * 3) ** 2` 결과. 첫 연산자도 곱셉 `*`이 아닌 지수 연산자 `'**'`이다.
- **D**: `256`는 `2 ** 8`. 표현식에 `3 ** 2`가 `9`라 지수는 `9`이다.

</detail>
