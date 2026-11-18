## 45.8 fetch

`fetch(url, options)`는 응답을 나타내는 `Response`로 이행되는 프로미스를 반환. URL만 전달하면 기본 메서드는 GET. 응답 본문을 읽는 `response.json()` 별도의 프로미스를 반환

### Response 객체

```js
fetch("https://jsonplaceholder.typicode.com/todos/1")
  .then((response) => {
    console.table({
      type: response.type,
      url: response.url,
      redirected: response.redirected,
      status: response.status,
      ok: response.ok,
      statusText: response.statusText,
      bodyUsed: response.bodyUsed,
    });
    if (!response.ok) {
      throw new Error("HTTP " + response.status);
    }
    return response.json();
  })
  .then((todo) => console.log(todo))
  .catch((error) => console.error(error.message));
```

| 속성         | 의미                             | 예                                             |
| ------------ | -------------------------------- | ---------------------------------------------- |
| `type`       | 응답 유형                        | `cors`                                         |
| `url`        | 응답 URL                         | `https://jsonplaceholder.typicode.com/todos/1` |
| `redirected` | 리디렉션 여부                    | `false`                                        |
| `status`     | HTTP 상태 코드                   | `200`                                          |
| `ok`         | 상태 코드가 200~299인지 여부     | `true`                                         |
| `statusText` | 상태 텍스트. 비어 있을 수 있음   | `""`                                           |
| `headers`    | 응답 헤더                        | `Headers` 객체                                 |
| `body`       | 응답 본문 스트림 또는 `null`     | 스트림                                         |
| `bodyUsed`   | 본문을 이미 읽기 시작했는지 여부 | 읽기 전 `false`                                |

**HTTP 404·500을 받아도 `fetch`가 자동으로 거부되지 않음** `response.ok`나 `response.status`를 확인
네트워크 오류나 요청 취소는 거부될 수 있음. `response.json()`은 본문을 읽어 JSON을 파싱, 잘못된 JSON이나 빈 본문에서 실패할 수 있음. 같은 응답 본문을 `json()`과 `text()` 연달아 읽지 않음

### 할 일 API로 GET·POST·PATCH·DELETE 요청

```js
async function requestJson(url, { method = "GET", body, signal } = {}) {
  const headers = { Accept: "application/json" };
  const options = { method, headers, signal };

  if (body !== undefined) {
    beaders["Content-Type"] = "application/json";
    options.body = JSON.stringify(body);
  }

  const response = await fetch(url, options);
  if (!response.ok) {
    throw new Error(method + " " + url + ": HTTP " + response.status);
  }
  if (response.status === 204) {
    return undefined;
  }

  const text = await response.text();
  if (!text.trim()) {
    return undefined;
  }
  return JSON.parse(text);
}
```

```js
async function runCrud() {
  console.log("GET", await requestJson("/todos/1"));

  const created = await requiestJson("/todos", {
    method: "POST",
    body: { content: "프로미스 실습", completed: false },
  });
  console.log("POST", created);

  if (created?.id === null) {
    throw new Error("생성 응답에 ID가 없다.");
  }
  const path = "/todos/" + encodeURIComponent(String(created.id));

  console.log(
    "PATCH",
    await requestJson(path, {
      method: "PATCH",
      body: { completed: true },
    }),
  );
  console.log("DELETE", await requestJson(path, { method: "DELETE" }));
}

runCrud().catch((error) => console.error(error.message));
```

요청 중간에 실패하면 생성한 항목이 남을 수 있음. `GET /todos`에 확인할 수 있음. DELETE 응답이 `200`과 `{}`이면 객체가 출력, `204`면 `undefined`가 출력된다. `requestJson`은 이 실습처럼 성공 본문이 JSON인 API를 전제로 한다

## 질문, 답변

### 1. 타이머 콜백에서 반환한 값을 바깥 함수가 받는지

- 아님. 콜백이 실행될 때 바깥 호출은 이미 종료

### 2. `resolve`를 호출하면 항상 즉시 `fulfilled`가 되는 지

- 아님. 대기 중인 프로미스를 너기면 결과를 기다림

### 3. `then`에서 다음 요청을 기다리면

- 콜백에서 요청의 프로미스를 반환

### 4. 타이머 예제에서 1보다 2와 3이 먼저인지

- 프로미스 후속 처리가 마이크로새트크에 먼저 실행되기 때문

### 5. 서버가 404를 반환하면 `fetch`의 `catch`가 자동으로 실행되는 지

- `response.ok`를 검사하고 직접 오류료 처리

[ECMAScript — Promise](https://tc39.es/ecma262/multipage/control-abstraction-objects.html#sec-promise-objects), [HTML 표준 — 이벤트 루프](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops), [Fetch 표준 — Response](https://fetch.spec.whatwg.org/#response-class), [MDN — Fetch 사용하기](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)
