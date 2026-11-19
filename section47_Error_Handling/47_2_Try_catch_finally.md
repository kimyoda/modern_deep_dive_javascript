## 47.2 try...catch...finally 문

| 블록           | 시점                                  | 용도                      |
| -------------- | ------------------------------------- | ------------------------- |
| `try`          | 실행                                  | 실패할 수 있는 코드       |
| `catch(error)` | `try`에 예외가 던져지면 실행          | 복구, 안내, 로깅, 재전파  |
| `finally`      | `try` 또는 `catch`를 벗어나기 전 실행 | 로딩 상태 해제, 자원 정리 |

```js
try {
  console.log("처리 시작");
  JSON.parse("{");
} catch (error) {
  console.log(error.name); // SyntaxError
} finally {
  console.log("정리 완료");
}

console.log("다음 작업");
```

- `try` 다음에 `catch` 또는 `finally`가 하나 이상 있어야 한다
- `try...finally` 만 쓰면 자원 정리 후에 처리하지 않은 예외는 상위로 전달
- `catch` 매개변수는 해당 블록 안에서만 쓸 수 있음. 에러 값을 읽지 않으면 `catch { ... }`도 가능
- `try`나 `catch`가 반환하거나 다시 던지더라도 `finally`가 먼저 실행. `finally`의 `return` 또는 `throw`가 앞선 결과를 덮어 쓸 수 있어 정리 작업에 집중
