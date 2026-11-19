## 47.4 throw 문

`new Error(...)`는 에러 객체를 만들고, `throw`는 값을 던져 실행 흐름을 바꿈. 자바스크립트는 문자열 같은 값도 던질 수 있으나, 일반적으로 이름·메시지·호출 경로가 있는 `Error` 객체를 던짐

```js
function repeat(count, callback) {
  if (typeof callback !== "function") {
    throw new TypeError("callback은 함수여야 한다.");
  }

  if (!Number.isInteger(count) || count < 0) {
    throw new RangeError("count는 0 이상의 정수여야 한다.");
  }

  for (let index = 0; index < count; index += 1) {
    callback(index);
  }
}

repeat(2, (index) => console.log(index)); // 0, 1

try {
  repeat(2, 1);
} catch (error) {
  console.error(error.name, error.message);
  // TypeError callback은 함수여야 한다
}
```

함수 안에 복구할 방법이 없고 호출자가 처리할 수 있도록 던짐. 원인을 유지하면서 설명을 추가할 때 `new Error('저장 실패', { cause: error})`처럼 감쌀 수 있음
