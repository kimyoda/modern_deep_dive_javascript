## 46.6 async/await

### 46.6.1 async 함수

`async` 함수는 항상 프로미스를 반환. 일반 값을 반환하면 이행하고, 예외를 던지면 거부. 선언문, 함수 표현식, 화살표 함수, 객체·클래스 메서드에 쓸 수 있음. 클래스 `constructor`에 붙일 수 없음

```js
async function declaration(value) { return value; }
const expression = async function (value) { return value; };
const arrow = async value => value;

const api = {
  async get(value) { return value; }
};

const TodoApi {
  async get(value) { return value; }
}

Promise.all([
  declaration(1),
  expression(2),
  arrow(3),
  api.get(4),
  new TodoApi().get(5)
]).then(console.log); // [1, 2, 3, 4, 5]
```

### 46.6.2 await 키워드

`await`는 해당 함수 실행을 잠시 멈춤. 다른 코드까지 멈추지 않음. 프로미스가 이행하면 값으로 재개, 거부되면 그 위치에 예외를 던진다. 일반 값도 `await` 할 수 있음. `async`함수 내부와 ES 모듈의 최상위에 사용할 수 있음

```js
async function fetchTodo(id) {
  const path = "/todos" + encodeURIComponent(String(id));
  const response = await fetch(path);

  if (!response.ok) {
    throw new Error("HTTP " + response.status);
  }

  return response.json();
}

fetchTodo(1)
  .then(console.log)
  .catch((error) => console.error(error.message));
```

#### 작업 간 의존 관계에 따른 실행 순서

```js
function delay(ms, value) {
  return new Promise((resolve) => setTimeout(() => resolve(value), ms));
}

async function independent() {
  // 새 작업을 먼저 시작하고 결과를 함께 기다림
  return Promise.all([delay(300, 1), delay(200, 2), delay(100, 3)]);
}

async function dependent(start) {
  const first = await delay(300, start);
  const second = await delay(200, first + 1);
  const third = await delay(100, second + 1);

  return [first, second, third];
}
```

| 상황                           | 방식                               | 시간     |
| ------------------------------ | ---------------------------------- | -------- |
| 앞선 결과가 필요 없음          | 작업을 먼저 시작하고 `Promise.all` | 약 300ms |
| 다음 작업에 앞선 결과가 필요함 | 순서대로 `await`                   | 약 600ms |

시간은 실행 환경에 따라 늘어날 수 있음. 앞선 요청의 사용자 ID를 알아야 두 번째 요청을 전송할 수 있어 순차 실행이 맞음

### 46.6.3 에러 처리

```js
async function fetchTodoWithFallback(id) {
  try {
    return await fetchTodo(id);
  } catch (error) {
    console.error(error.message);
    return null;
  }
}

fetchTodo(1).catch((error) => console.error("호출자 처리:", error.message));
```

함수 내부에 거부를 잡으면 `try`안에 프로미스를 `await` 한다. `return fetchTodo(id)`처럼 기다리지 않고 프로미스를 반환하면, 프로미스 거부는 호출자가 반환값에서 처리할 수 있음. 타이머 콜백에서 나중에 던진 예외는 바깥 `try`에 자동으로 들어가지 않음

## 질문, 답변

### 1. 제너레이터 함수를 호출하면 본문이 바로 실행되는지

- 아님. 객체를 얻고 첫 `next()`를 호출해야 본문이 실행.

### 2. `next(value)`의 값은 어디로 전달되는 지

- 직전에 멈춘 `yield` 표현식 결과가 된다

### 3. `await`는 브라우저 실행 전체를 멈추는 지

- 아님. 해당 함수의 다음 실행을 미룸

### 4. 언제 `Promise.all`을 사용하는지

- 서로 의존하지 않고, 모든 결과가 필요한 작업을 함께 시작할 때 사용

### 5. `await`한 프로미스가 거부되면

- 위치에서 예외가 발생. `try...catch`로 처리하거나 호출자에게 전달.

[MDN — Generator](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Generator),
[MDN — await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/await)
