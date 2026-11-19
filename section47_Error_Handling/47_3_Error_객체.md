## 47.3 Error 객체

`new Error('메시지')`는 에러 객체를 만듬. 객체 생성만으로 현재 흐름이 중단되지 않음

```js
const error = new Error("저장에 실패");

console.log(error.name); // Error
console.log(error.message); // 저장에 실패했습니다
console.log(error.stack); // 호출 경로를 추적할 때 사용
```

| 생성자           | 발생 상호아 또는 쓰임                                 | 예시                              |
| ---------------- | ----------------------------------------------------- | --------------------------------- |
| `Error`          | 일반적인 실패를 표현                                  | `new Error('저장 실패')`          |
| `SyntaxError`    | 문법에 맞지 않는 코드르 해석하거나 잘못된 JSON을 파싱 | `JSON.parse('{')`                 |
| `ReferenceError` | 참조할 수 없는 식별자 접근                            | 선언하지 않은 `missingFunction()` |
| `TypeError`      | 타입이나 연산 대상이 맞지 않음                        | `null.value`                      |
| `RangeError`     | 허용 범위를 벗어난 값                                 | `new Array(-1)`                   |
| `URIError`       | URI 인코딩·디코딩 입력이 올바르지 않음                | `decodeURIComponet('%')`          |
| `EvalError`      | 호환성을 위해 유지되는 에러 타입                      | `new EvalError('예시')`           |
| `AggregateError` | 여러 실패를 하나로 묶음. 45장 `Promise.any`와 연결    | `Promise.any([])` 의 거부 이유    |

같은 문법 오류도, 스크립트 자체가 파싱되지 않으면 스크립트의 `try`까지 실행되지 않음. 위 `JSON.parse` 예제는 이미 실행 중인 코드가 호출하는 함수 안에서 발생하여 잡을 수 있음. `stack`의 표시 형식은 실행 환경에 따라 다름
