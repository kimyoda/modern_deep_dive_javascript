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

### 2. 다음 코드 출력 순서는?

```js
console.log(score);
var score = 80;
console.log(score);
```

- A. `80` -> `80`
- B. 첫 줄에 `ReferenceError` 발생
- C. `undefined` -> `80`
- D. `undefined` -> `undefined`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `80`은 `var score = 80;`이 실행될 때 할당. 첫 출력은 그 전에 실행.
- **B**: `var`로 선언한 `score`는 첫 줄이 실행되기 전에 이미 `undefined`로 초기화. 첫 줄에서도 읽을 수 있음.
- **C**: `var` 선언이 본문 실행 전에 처리되는 것이 변수 호이스팅. 첫 출력은 초기값 `undefined`를 읽고, 두 번째 출력은 할당된 값 `80`을 읽는다.
- **D**: 마지막 출력 전에 `score`에 이미 `80`이 할당돼 있음.

</detail>

### 2. 다음 코드 출력 순서는?

```js
console.log(score);
var score = 80;
console.log(score);
```

- A. `80` -> `80`
- B. 첫 줄에 `ReferenceError` 발생
- C. `undefined` -> `80`
- D. `undefined` -> `undefined`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `80`은 `var score = 80;`이 실행될 때 할당. 첫 출력은 그 전에 실행.
- **B**: `var`로 선언한 `score`는 첫 줄이 실행되기 전에 이미 `undefined`로 초기화. 첫 줄에서도 읽을 수 있음.
- **C**: `var` 선언이 본문 실행 전에 처리되는 것이 변수 호이스팅. 첫 출력은 초기값 `undefined`를 읽고, 두 번째 출력은 할당된 값 `80`을 읽는다.
- **D**: 마지막 출력 전에 `score`에 이미 `80`이 할당돼 있음.

</detail>

### 3. 다음 코드에서 실행 중 에러가 발생하는 줄은 어디인지?

```js
let count = 1;
const limit = 3;
count = 2;
limit = 4;
```

- A. `let count = 1`
- B. `const limit = 3;`
- C. `count = 2;`
- D. `limit = 4;`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: `let`으로 변수를 선언하면 초기값 `1`을 지정할 수 있음.
- **B**: `const` 선언 시점에 초기값 `3`을 지정하므로 정상적으로 실행된다.
- **C**: `let`으로 선언한 변수는 재할당할 수 있어 `count`가 `2`로 바뀐다.
- **D**: `const`로 선언한 `limit`에 새 값을 재할당하면 `TypeError`가 발생.

</detail>

### 4. 객체가 가비지 컬렉션 대상이 되는 조건은 무엇인지

- A. 실행 중인 코드에 강한 참조를 따라 객체에 더 이상 도달할 수 없음
- B. 같은 객체를 가리키는 두 변수 중 하나만 다른 값으로 바뀌었음
- C. 객체의 프로퍼티를 모두 삭제했다
- D. 객체를 생성한 함수의 실행이 끝남

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: 더 이상 도달할 수 없는 객체의 메모리는 가비지 컬렉터가 회수할 수 있음.
- **B**: 다른 변수에 참조가 남아 있으므로 그 변수를 통해 객체에 접근할 수 있음.
- **C**: 프로퍼티를 모두 삭제해도 변수가 그 객체를 참조하고 있으면 빈 객체에 접근할 수 있음
- **D**: 함수가 반환한 객체를 외부 변수에 저장했으면, 함수 실행이 끝난 뒤에도 객체에 접근할 수 있음

</detail>

### 5. 자바스크립트에서 변수 이름으로 쓸 수 있는 것은?

- A. `1stScore`
- B. `user-score`
- C. `_score2`
- D. `class`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: 숫자는 식별자의 첫 글자 뒤에 쓸 수 있음. `1stScore`는 첫 글자가 숫자여서 문법 오류가 발생.
- **B**: `-`는 연산자 기호로 해석. 두 단어를 변수 이름 하나로 쓰려면 `userScore`처럼 붙여 쓸 수 있음.
- **C**: `_`로 식별자를 시작할 수 있고, 첫 글자 뒤에는 숫자도 쓸 수 있음
- **D**: `class`는 클래스 선언에 사용하는 예약어라 변수 이름으로 사용하면 문법 오류가 발생

</detail>

### 6. 각 줄에 명시적으로 작성한 동작을 위에서부터 나열한 것은?

```js
var level; // ①
level = 1; // ②
level = 2; // ③
```

- A. 선언 -> 첫 명시적 할당 -> 재할당
- B. 선언 -> 재선언 -> 재할당
- C. 할당 -> 선언 -> 참조
- D. 선언 -> 할당 -> 참조

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: ①에서 변수를 선언, ②에서 코드에 적은 첫 값 `1`을 할당. ③에서 기존 값을 `2`로 바뀜
- **B**: ②는 이미 선언한 변수에 `1`을 저장하는 할당
- **C**: 변수를 선언하는 부분은 `var`가 붙은 ①이다. ②와 ③의 `=`는 값을 저장.
- **D**: ③은 기존 값을 읽는 코드가 아님. `level`에 새 값 `2`를 재할당.

</detail>

### 7. 다음 코드의 첫 줄을 실행하면 어떻게 되는지

```js
console.log(attempts);
let attempts = 10;
```

- A. `undefined`를 출력
- B. `10`을 출력
- C. `SyntaxError`가 발생
- D. `ReferenceError`가 발생

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: `undefined`로 초기화된 변수를 읽으면 `undefined`가 나온다. 이 시점의 `attempts`는 초기화되기 전.
- **B**: `10`으로 초기화하는 부분은 둘째 줄에 있음. 첫 줄에서 에러가 발생해 그 줄까지 실행이 이어지지 않음.
- **C**: 두 줄의 문법 자체는 유효. 에러는 첫 줄에 변수의 값을 읽는 도중 발생.
- **D**: 첫 줄은 `attempts`의 일시적 사각지대(TDZ)에 발생. `let` 변수를 초기화 전에 읽으면 `ReferenceError`가 발생

</detail>

### 8. 다음 코드는 어떤 두 값을 출력하는 지?

```js
var score = 10;
var Score = 20;
console.log(score, Score);
```

- A. `20 20`
- B. `10 20`
- C. `10 10`
- D. `ReferenceError` 발생

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `20`은 대문자 `S`로 시작하는 `Score`할당. `score`값 `10`
- **B**: 자바스크립트는 식별자의 대소문자를 구분. `score`와 `Score`는 각각 별개 변수.
- **C**: 두 번째 인수인 `Score`는 둘째 줄에 `20`으로 초기화
- **D**: 출력이 실행될 때 두 변수 모두 선언과 초기화가 끝난 상태.

</detail>

### 9. 네이밍 컨벤션에 맞는 변수 이름과 클래스 이름 조합은

- A. 변수 `UserName` / 클래스 `userProfile`
- B. 변수 `user_name` / 클래스 `user_profile`
- C. 변수 `USER_NAME` / 클래스 `USER_PROFILE`
- D. 변수 `userName` / 클래스 `UserProfile`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: 변수 이름은 파스칼 케이스, 클래스 이름은 카멜 케이스로 작성된 조합.
- **B**: 두 이름 모두 단어를 밑줄로 구분하는 스네이크 케이스
- **C**: 두 이름 모두 대문자와 밑줄을 사용한 표기
- **D**: 변수에 소문자로 시작하는 카멜 케이스, 클래스에 대문자로 시작하는 파스칼 케이스를 적용

</detail>

### 10. 다음 코드 출력 결과는

```js
function showScore() {
  if (true) {
    var score = 80;
  }

  console.log(score);
}

showScore();
```

- A. `80`
- B. `undefined`
- C. `ReferenceError` 발생
- D. 출력 없이 종료

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: 함수 안에서 `var`로 선언한ㅁ 변수 스코프는 함수 전체. 같은 함수 `if` 블록 밖에서 `score`를 읽을 수 있음
- **B**: 조건이 `true`여 블록이 실행, 출력하기 전에 이미 `80`이 할당
- **C**: `console.log(score)`는 `score`를 선언한 함수 스코프 안에 있음
- **D**: 마지막 줄에서 `showScore()`를 호출하여 함수 안의 출력문이 실행.

</detail>
