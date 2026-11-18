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

### 46.6.3 에러 처리
