## 45장 프로미스

> **프로미스는 나중에 사용할 결과나 실패 이유를 담는 객체. 결과가 필요한 작업은 `then` 또는 `await` 뒤에 둔다**

## 45.1 비동기 처리를 위한 콜백 패턴 단점

### 45.1.1 콜백 헬

비동기 작업을 시작하는 함수, 나중에 실행되는 콜백은 호출 시점이 다름. 타이머나 XHR 응답 핸들러에 값을 반환해도 그 값이 바깥 함수의 반환값이 되지 않음. 바깥 변수에 대입하는 것은 가능하나, 대입 직전에 읽으면 아직 값이 들어오지 않음

```js
function readLater() {
  setTimeout(() => {
    return "응답"; // 타이머 콜백 반환값
  }, 0);
}

console.log(readLater()); // undefined

let result;
setTimeout(() => {
  result = "응답";
  console.log(result); // 나중에: 응답
}, 0);
console.log(result); // 먼저: undefined
```

콜백을 인수로 받아 응답 뒤에 실행, 그 시점에 결과를 읽을 수 있음. 앞선 결과에 의존하는 요청이 계속 추가되면 성공과 실패 분기가 중첩

```js
function readLater() {
  setTimeout(() => {
    return "응답"; // 타이머 콜백 반환값
  }, 0);
}

console.log(readLater()); // undefined

let result;
setTimeout(() => {
  result = "응답";
  console.log(result); // 나중에: 응답
}, 0);
console.log(result); // 먼저: undefined
```

콜백을 인수로 받아 응답 뒤에 실행, 그 시점에 결과를 읽을 수 있음. 앞선 결과에 의존하는 요청이 게속 추가되면 성공과 실패분기가 중첩

```js
// get(url, onSuccess, onFailure)가 결과 또는 오류를 콜백에 전달한다고 가정
const onFailure = (error) => console.error(error);

get(
  "/posts/1",
  ({ userId }) => {
    get(
      "/users/" + userId,
      (user) => {
        console.log(user);
      },
      onFailure,
    );
  },
  onFailure,
);
```

### 45.1.2 에러 처리 한계

외부 `try...catch`는 블록이 실행되는 동안 발생한 예외를 처리. 타이머 콜백이 별도의 실행 흐름에 나중에 예외를 던지면 이미 종료된 바깥 `catch`까지 전파되지 않음

```js
// 동작을 확인하기 위한 예제. 단독 실행 시 처리되지 않은 예외가 발생
try {
  setTimeout(() => {
    throw new Error("타이머 콜백에서 발생한 오류");
  }, 0);
} catch (error) {
  console.error("코드에서 실행되지 않음:", error.message);
}
```

콜백 안에서 오류를 직접 처리, 실패를 `reject(error)`로 전달해 프로미스 체인의 `catch`에 다룸
