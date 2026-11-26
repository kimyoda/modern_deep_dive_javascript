## section08 제어문

### 1. 어떤 값이 출력되는지

```js
let count = 1;
{
  let count = 2;
  console.log(count);
}
console.log(count);
```

- A. `2` -> `2`
- B. `2` -> `1`
- C. `1` -> `2`
- D. `1` -> `1`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: 안쪽 선언은 바깥 변수의 값을 바꾸지 않음
- **B**: 안쪽에서 블록의 `count`, 밖에서 바깥 `count`를 읽음
- **C**: 블록 안의 출력문이 먼저 실행
- **D**: 블록 안에서 가까운 선언 값 `2`를 읽음

</detail>

### 2. 어느 등급 출력

```js
const score = 95;

if (score >= 90) {
  console.log("A");
} else if (scorer >= 80) {
  console.log("B");
} else {
  console.log("C");
}
```

- A. `'B'`
- B. `'C'`
- C. `'A'` -> `'B'`
- D. `'A'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: `80`이상이기도 하지만 앞 조건이 먼저 참
- **B**: `else`는 앞 조건이 모두 거짓일 때 실행
- **C**: 하나의 `if...else if`에서 첫 참인 분기만 실행
- **D**: `95 >= 90`이 참이므로 첫 분기만 실행

</detail>

### 3. 다음 `switch`문 출력 순서는?

```js
switch ("pro") {
  case "pro":
    console.log("프로");
  case "basic":
    console.log("기본");
    break;
  default:
    console.log("기타");
}
```

- A. `'프로'` -> `'기본'`
- B. `'프로'`
- C. `'프로'` -> `'기본'` -> `'기타'`
- D. `'기본'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: `'pro'`에서 시작해서 `break`를 만날 때까지 문장을 실행
- **B**: 첫 분기 뒤 `break`가 없어 아래로 이어짐
- **C**: 두 번째 분기 `break`가 `default` 진입을 막음
- **D**: 처음 일치한 항목은 `'pro'`이므로 첫 출력도 실행

</detail>

### 4. 문자열 `'1'`을 검사하면 무엇이 출력인지

```js
switch ("1") {
  case 1:
    console.log("숫자");
    break;
  case 2:
    console.log("문자열");
    break;
  default:
    console.log("기타");
}
```

- A. `'숫자'`
- B. `'기타'`
- C. `'문자열'`
- D. `'숫자'` -> `'문자열'`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `switch`는 숫자 `1`과 문자열 `'1'`을 같은 값으로 보지 않음
- **B**: `switch`는 문자열 `'1'`을 숫자 `1`이나 `2`로 변환해서 비교하지 않음. 일치하는 `case`가 없으므로 `default`가 실행되어 `'기타'`가 출력.
- **C**: `case`를 찾을 때 타입을 변환하지 않고 `case '1'`이 선택
- **D**: 숫자 분기에 진입하지 않고 문자열 분기 뒤의 `break`에서 끝남

</detail>

### 5. 반복이 끝난 뒤 `sum`의 값

```js
let sum = 0;

for (let i = 1; i <= 4; i += 1) {
  sum += i;
}

console.log(sum);
```

- A. `6`
- B. `10`
- C. `15`
- D. `4`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `6`은 `1 + 2 + 3`이다. 조건이 `<=` `4`도 더해야한다
- **B**: `i`가 `1`, `2`, `3`, `4`일 때 본문 실행
- **C**: `i`가 `5`일 때 조건이 거짓이라 더하지 않음
- **D**: `4`는 마지막으로 더한 값. `sum`은 누적된 합

</detail>

### 6. `while` 본문은 몇 번 실행인지

```js
let n = 5;
let runs = 0;

while (n < 3) {
  runs += 1;
  n += 1;
}

console.log(runs);
```

- A. `1`번
- B. `2`번
- C. `3`번
- D. `0`번

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: `while`은 첫 실행 전에 조건을 확인
- **B**: 처음부터 조건이 거짓이라 두 번 실행될 수 없음
- **C**: 비교에 쓰인 `3`이 반복 횟수를 뜻하지 않음
- **D**: 처음 `5 < 3`ㅇ 거짓으로 본문을 건너띔

</detail>

### 7. `do...while` 본문은 몇 번 실행될 지

```js
let runs = 0;

do {
  runs += 1;
} while (false);

console.log(runs);
```

- A. `1`번
- B. `0`번
- C. `2`번
- D. 계속 반복

<detail>
<summary>정답과 해설 보기</summary>

**정답 A**

- **A**: 본문을 한 번 실행한 뒤 조건이 거짓이라 종료
- **B**: 조건을 먼저 확인하는 `while`의 동작과 구분
- **C**: 첫 번째 실행 뒤 조건이 거짓이라 두 번째 실행은 없음
- **D**: 조건이 `false`므로 다시 반복하지 않음

</detail>

### 8. 안쪽 반복문의 `break` 뒤 최종적으로 무엇이 출력인지?

```js
let count = 0;

for (let row = 0; row < 2; row += 1) {
  for (let col = 0; col < 3; col += 1) {
    if (col === 1) {
      break;
    }
    count += 1;
  }
}

console.log(count);
```

- A. `0`
- B. `1`
- C. `2`
- D. `6`

<detail>
<summary>정답과 해설 보기</summary>

**정답 C**

- **A**: 각 행에서 `col`이 `0`일 때 증가문을 실행
- **B**: 레이블 없는 `break`는 바깥 반복문까지 끝내지 않음
- **C**: 각 행에서 `col === 0`일 때 한 번씩 증가. 두 행이므로 총 두번.
- **D**: 각 행의 `col ==== 1` 부터 안쪽 반복이 중단.

</detail>

### 9. `continue`가 있을 때 출력되는 합은 얼마인지

```js
let sum = 0;

for (let i = 1; i <= 5; i += 1) {
  if (i % 2 === 0) {
    continue;
  }
  sum += i;
}

console.log(sum);
```

- A. `6`
- B. `9`
- C. `15`
- D. `1`

<detail>
<summary>정답과 해설 보기</summary>

**정답 B**

- **A**: `6`은 건너 뛴 짝수 `2 + 4`의 합
- **B**: `continue`가 짝수의 덧셈만 건너뛰어 `1 + 3 + 5`가 남는다
- **C**: `2`, `4`는 더하지 않음
- **D**: `continue`는 전체 반복을 끝내지 않아 `3`과 `5`도 더함

</detail>

### 10. `break outer`를 만나면 최종 출력은?

```js
let count = 0;

outer: for (let row = 0; row < 3; row += 1) {
  for (let col = 0; col < 3; col += 1) {
    if (row === 1 && col === 1) {
      break outer;
    }
    count += 1;
  }
}

console.log(count);
```

- A. `3`
- B. `5`
- C. `7`
- D. `4`

<detail>
<summary>정답과 해설 보기</summary>

**정답 D**

- **A**: `row === 1`에서 `col === 0` 일 때 한 번 더 증가
- **B**: `row === 1`, `col === 1` 에서 증가문보다 `break`가 먼저
- **C**: 바깥 반복에 붙은 `outer`를 종료하여 세 번째 행은 실행되지 않음
- **D**: 첫 행에서 세 번, 둘째 행의 첫 칸에서 한 번 증가.

</detail>
