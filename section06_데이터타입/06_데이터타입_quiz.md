## section06 데이터 타입

### 1. 다음 코드의 출력 결과는?

```js
console.log(typeof 10, typeof 10.5, typeof 10n);
```

- A. `'integer'`, `'number'`, `'bigint'`
- B. `'number'`, `'number'`, `''bigint'`
- C. `'number'`, `'float'`, `'bigint'`
- D. `'number'`, `'number'`, `'number'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `10`은 정수지만 `typeof`는 `'integer'`가 아닌 `'number'`를 반환.
- **B**: `10`과 `10.5`는 모두 Number 타입. 뒤에 `n`이 붙은 `10n`은 Bigint 타입
- **C**: `10.5`처럼 소수 부분이 있어도 `typeof` 결과는 `'number'``이다.
- **D**: `10n`은 Number와 구분되는 BigInt 값이라서 세 번째 결과는 `'bigint'`이다

</detail>

### 2. 다음 코드 출력 결과는

```js
console.log(1 / 0, 0 / 0, typeof NaN);
```

- A. `NaN`, `NaN`, `'number'`
- B. `Infinity`, `0`, `'number'`
- C. `Infinity`, `NaN`, `'undefined'`
- D. `Infinity`, `NaN`, `'number'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: Number 값 `1`을 `0`으로 나누면 양의 무한대를 나타내는 `Infinity`가 나온다.
- **B**: `0 / 0`은 `0`이 아니라 `NaN`으로 평가.
- **C**: `NaN`은 유효한 수치 결과가 없음을 나타내는 Number 값. `undefined`와 다르다
- **D**: `1 / 0`은 `Infinity`고, `0 / 0`은 `NaN`이다. 둘 다 Number 타입에 속한다

</detail>

### 3. 다음 코드에 두 변수의 값은?

```js
const original = "cat";
const changed = original.replace("c", "b");
console.log(original, changed);
```

- A. `'cat'`, `'bat'`
- B. `'bat'`, `'bat'`
- C. `'cat'`, `'cat'`
- D. `'cat'`, `undefined`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: 문자열은 불변. `replace()`는 원본을 바꾸지 않고 치환 결과인 새 문자열 `'bat'`을 반환
- **B**: `replace()`는 `original`이 가리키는 문자열을 직접 변경하지 않음.
- **C**: 원본은 유지, `changed`에 치환 결과인 `'bat'`이 들어감.
- **D**: `replace()`는 반환값이 있는 메서드. 코드에서 새 문자열을 반환

</detail>

### 4. 다음 템플릿 리터럴 결과는?

```js
const price = 3000;
const quantity = 2;
const message = `합계: ${price * quantity}원`;

console.log(message);
```

- A. `'합계: 30002원'`
- B. `'합계: ${price * quantity}원'`
- C. `'합계: 6000원'`
- D. `6000`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: `price * quantity`에 곱셈 연산자가 있어 두 숫자를 이어 붙이지 않음.
- **B**: 코드에서 백틱을 썼기 때문에 `${...}` 내부 표현식을 평가해 결과를 넣는다
- **C**: `3000 * 2`를 평가한 결과 `6000`이 문자열 안에 들어가 `'합계: 6000원'`이 된다
- **D**: 곱셈의 결과만 보면 숫자 `6000`이지만 템플릿 리터럴 전체의 결과는 문자열.

</detail>

### 5. 다음 코드에서 두 변수의 타입은 각각 무엇인지

```js
const isOpen = false;
const label = "false";
```

- A. 불리언, 불리언
- B. 불리언, 문자열
- C. 문자열, 불리언
- D. 문자열, 문자열

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `label`의 값에 따옴표가 있으므로 불리언이 아니라 문자열
- **B**: 따옴표 없는 `false`는 불리언 리터럴, `'false'`는 문자 다섯 개로 이루어진 문자열
- **C**: 두 값을 반대로 구분. 첫 번째 값은 불리언이고 두 번째 값은 문자열
- **D**: `isOpen`에 문자열이 아닌 불리언 값 `false`가 들어 있음

</detail>

### 6. 다음 코드 출력 결과는 무엇인지

```js
let current;
let selected = null;

console.log(current, selected, typeof selected);
```

- A. `null`, `null`, `'object'`
- B. `undefined`, `null`, `'null'`
- C. `undefined`, `undefined`, `'undefined'`
- D. `undefined`, `null`, `'object'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: 초기값을 지정하지 않은 `let current;`는 선언문이 실행된 뒤 `undefined` 값을 가진다
- **B**: `typeof null`의 실제 결과는 `'null'`이 아니라 `'object'`이다
- **C**: `selected`에 값이 없음을 나타내 `null`을 명시적으로 넣음. `undefined`로 바뀌지 않음
- **D**: `current`는 `undefined`고 `selected`는 `null`이다. `typeof null`은 `'object'`지만 `null` 자체는 원시 값.

</detail>

### 7. 다음 코드의 출력 결과는

```js
const first = Symbol("id");
const second = Symbol("id");

console.log(first === second, typeof first);
```

- A. `false`, `'symbol'`
- B. `true`, `'symbol'`
- C. `false`, `'string'`
- D. `true`, `'object'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: `Symbol()`을 각각 호출하면 서로 다른 심벌이 만들어진다. 설명이 같아도 비교 결과는 `false`고 타입은 `'symbol'`이다
- **B**: 설명 문자열이 같아도 `Symbol()`이 같은 값을 돌려주지 않음
- **C**: `'id'`는 심벌에 붙인 설명. `first`에 저장되는 값은 문자열이 아닌 심벌
- **D**: 두 값은 서로 다르고, 심벌은 객체가 아닌 원시값

</detail>

### 8. 다음 코드를 정확하게 설명한 것은

```js
const todo = { completed: false };
todo.completed = true;
console.log(todo.completed);
```

- A. `const`로 선언했으므로 둘째 줄에 `TypeError`가 발생
- B. 첫 줄에 `false`를 저장했으므로 `false`가 출력
- C. `todo`는 같은 객체를 가리키고, 프로퍼티 값이 바뀌어 `true`가 출력
- D. 둘째 줄이 새 객체를 만들어 `todo`에 재할당

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: `const`는 `todo`에 다른 값을 재할당하지 못하게함. 객체의 프로퍼티 변경은 막지않음.
- **B**: 둘째 줄이 `completed` 프로퍼티를 `true`로 변경한 뒤에 출력.
- **C**: `todo`의 바인딩은 유지. 같은 객체 안의 `completed` 값만 바뀜.
- **D**: `todo.completed = true`는 기존 객체의 프로퍼티에 값을 할당하는 코드

</detail>

### 9. 자바스크립트에서 데이터 타입을 구분하는 이유로 적절한 것은?

- A. 따옴표로 감싼 숫자 모양의 값을 모두 숫자로 저장하기 위함
- B. 값을 어떻게 표현하고 해석할 지, 연산에서 어떻게 처리할 지 구분하기 위함
- C. 변수에 들어갈 값의 타입을 선언 시점에 하나로 고정하기 위함
- D. 같은 연산자를 쓰면 어떤 타입의 값이 와도 동일하게 처리하기 위함

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `'20'`은 숫자처럼 보여도 문자열. 데이터 타입은 이런 값을 숫자 `20`과 구분
- **B**: 예를 들어 `20 + 1`은 숫자 덧셈, `'20 + 1'`은 문자열 연결. 타입에 따라 값을 해석하고 연산하는 방식이 다름
- **C**: 자바스크립트에서 같은 변수에도 다른 타입의 값을 다시 할당할 수 있음
- **D**: `+`처럼 같은 연산자도 피연산자의 타입에 따라 다르게 동작할 수 있음

</detail>

### 10. 다음 코드 출력 순서는?

```js
let result = 42;
console.log(typeof result);

result = "완료";
console.log(typeof result);
```

- A. `'number'` -> `'number'`
- B. `'number'` 출력 후 `TypeError` 발생
- C. `'string'` -> `'string'`
- D. `'number'` -> `'string'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: 재할당 뒤 `result`에 저장된 값은 숫자가 아닌 문자열
- **B**: `let` 변수에 다른 타입의 값을 재할당하는 것은 허용
- **C**: 첫 번째 출력 시점에 `result`에 아직 숫자 `42`가 들어 있음
- **D**: 첫 번째는 숫자 값의 타입을, 두 번째는 문자열 값의 타입을 확인. 이런 특성을 동적 타이핑이라 함

</detail>
