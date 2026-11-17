## 43.3 XMLHttpRequest

### 43.3.1 생성과 요청

```js
const xhr = new XMLHttpRequest();
const query = new URLSearchParams({ completed: "true" });

xhr.open("GET", "/todos?" + query); // 세 번째 인수 생략 시 비동기
xhr.setRequestHeader("Accept", "application/json");
xhr.onload = () => console.log(xhr.status, xhr.responseText);
xhr.onerror = () => console.error("네트워크 요청 실패");
xhr.send();
```

순서는 **생성 -> open -> 헤더와 이벤트 처리 설정 -> send**다. `open(method, url, async)`에서 `async` 기본값은 `true`다. `open`은 요청을 준비, 실제 전송은 `send`가 시작. 조회 조건은 경로나 쿼리 문자열로 보냄. XHR에 GET·HEAD의 `send`에 넣은 본문 인수는 무시된다

| 헤더           | 의미                             | 예                 |
| -------------- | -------------------------------- | ------------------ |
| `Content-Type` | 현재 메시지 본문 형식            | `application/json` |
| `Accept`       | 클라이언트가 받고 싶은 응답 형식 | `application/json` |

JSON 본문을 보낼 때 `setRequestHeader('Content-Type', 'application/json')`과 `send(JSON.stringify(data))`를 함께 쓴다. HTML 응답은 `text/html`, 일반 텍스트는 `text/plain`을 사용한다. `FormData`로 파일을 보낼 때 브라우저가 multipart 경계값을 포함한 헤더를 만들도록 둔다

### HTTP/1.1 요청과 응답

**요청 예시**

```http
POST /todos HTTP/1.1
Host: localhost:3000
Content-Type: application/json
Accept: application/json
Content-Length: 39

{"content": "Angular", "completed": false}
```

**응답 예시**

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /todos/4
Content-Length: 46
{"id":4, "content":"Angular","completed":false}
```

| 부분  | 요청                          | 응답                          |
| ----- | ----------------------------- | ----------------------------- |
| 시작  | 메서드·요청 대상·HTTP 버전    | HTTP 버전·상태 코드·이유 문구 |
| 헤더  | Host, Content-Type, Accept 등 | Content-Type, Location 등     |
| 빈 줄 | 헤더와 본문 구분              | 헤더와 본문 구분              |
| 본문  | 생성할 할 일의 표현           | 서버가 돌려준 생성 결과       |

HTTP/1.1 시작 줄과 헤더 줄은 CRLF로 끝남. `Content-Length`는 마지막 개행을 제외한 본문 바이트 수.

### 43.3.2 상태와 이벤트

| readyState | 이름               | 의미                                    |
| ---------: | ------------------ | --------------------------------------- |
|          0 | `UNSENT`           | `open` 호출 전                          |
|          1 | `OPENED`           | `open` 호출 후                          |
|          2 | `HEADERS_RECEIVED` | 응답 헤더와 상태 코드 확인 가능         |
|          3 | `LOADING`          | 응답 본문 수신 중                       |
|          4 | `DONE`             | 요청 처리 종료. 성공 여부는 별도로 확인 |

| 항목                                 | 용도                                                                         |
| ------------------------------------ | ---------------------------------------------------------------------------- |
| `status`, `statusText`               | HTTP 상태 코드와 상태 문구. 성공 판정에 숫자 코드를 사용                     |
| `responseType`, `response`           | 응답 해석 방식과 결과. 예: `json`, `text`, `blob`, `arraybuffer`, `document` |
| `responseText`                       | 텍스트로 읽는 응답 본문                                                      |
| `readystatechange`                   | `readyState`가 바뀔 때                                                       |
| `loadstart`, `progress`              | 요청 시작과 전송 진행 상황                                                   |
| `load`                               | 응답 수신 완료. HTTP 400·500도 이 이벤트로 받을 수 있음                      |
| `error`, `timeout`, `abort`          | 네트워크 오류, 시간 초과, 취소                                               |
| `loadend`                            | 성공·오류·시간 초과·취소로 요청이 끝날 때                                    |
| `abort()`, `getResponseHeader(name)` | 진행 중인 요청 취소, 응답 헤더 조회                                          |

### 43.3.3 응답 처리 실습

`public/ajax.html`에 저장한다. 서버를 켠 뒤 `http://127.0.0.1:3000/ajax.html`에서 조회 버튼을 누른다. `load`를 쓰므로 핸들러에서 `DONE`을 다시 검사할 필요가 없다.

```html
<!doctype html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <title>Ajax 조회</title>
  </head>
  <body>
    <button id="load" type="button">할 일 조회</button>
    <pre id="result" aria-live="polite"></pre>
    <script>
      const result = document.getElementById("result");

      document.getElementById("load").addEventListener("click", () => {
        const xhr = new XMLHttpRequest();
        xhr.open("GET", "/todos/1");
        xhr.setRequestHeader("Accept", "application/json");
        xhr.timeout = 5000;

        xhr.onload = () => {
          if (xhr.status < 200 || xhr.status >= 300) {
            result.textContent = "HTTP 오류: " + xhr.status;
            return;
          }

          try {
            const todo = JSON.parse(xhr.responseText);
            result.textContent = JSON.stringify(todo, null, 2);
          } catch (error) {
            result.textContent = "JSON 응답을 읽을 수 없습니다.";
          }
        };

        xhr.onerror = () => {
          result.textContent = "네트워크 요청 실패";
        };
        xhr.ontimeout = () => {
          result.textContent = "요청 시간 초과";
        };
        result.textContent = "조회 중...";
        xhr.send();
      });
    </script>
  </body>
</html>
```

`readystatechange` 방식으로 연습하려면 `load` 핸들러 대신 아래 형태로 등록한다. 두 방식의 완료 로직을 한 요청에 중복으로 넣지 않는다.

```js
xhr.onreadystatechange = () => {
  if (xhr.readyState !== XMLHttpRequest.DONE) {
    return;
  }
  if (xhr.status === 0) {
    return; // 네트워크 오류 등은 별도 이벤트에서 처리
  }
  console.log(xhr.status, xhr.responseText);
};
```

### fetch 비교

```js
async function getTodo() {
  const response = await fetch("/todos/1");

  if (!response.ok) {
    throw new Error("HTTP " + response.status);
  }
  return response.json();
}
```

`fetch`도 HTTP 404를 받았다는 이유만으로 Promise를 거부하지 않음. `response.ok`를 먼저 확인. 개발 서버와 API 출처가 다르면 프록시 또는 서버 CORS 설정이 필요할 수 있음

[XMLHttpRequest 표준](https://xhr.spec.whatwg.org/)
[RFC 8259 — JSON](https://www.rfc-editor.org/rfc/rfc8259.html)
[RFC 9112 — HTTP/1.1](https://www.rfc-editor.org/rfc/rfc9112.html)
