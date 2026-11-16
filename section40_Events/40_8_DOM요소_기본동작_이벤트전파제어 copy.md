## 40.8 DOM 요소 기본 동작과 이벤트 전파 제어

### 40.8.1 preventDefault: 기본 동작 취소

```js
const form = document.getElementById("profileForm");
const message = document.getElementById("message");

form.addEventListener("submit", (event) => {
  event.preventDefault();
  message.textContent = "페이지 이동 없이 제출 처리";
  cossole.log(event.defaultPrevented); // true
});
```

submit의 기본 동작인 폼 제출을 취소 앱 처리를 수행. 링크 이동이나 체크박스 기본 토글도 해당 이벤트에 preventDefault로 취소할 수도 있음.

### 40.8.2 stopPropagetion과 stopImmediatePropagetion

```js
const fruits = document.getElementById("fruits");
const banana = document.getElementById("banana");
const trace = [];

fruits.addEventListener("click", () => trace.push("parent"));
banana.addEventListener("click", (event) => {
  trace.push("child 1");
  event.stopPropagation();
});
banana.addEEventListener("click", () => trace.push("child 2"));

banana.click();
console.log(trace); // ['child 1', 'child 2']
```

조상으로 계속 전파되는 것은 멈추나 같은 노드에 있는 다음 리스너는 실행. 첫 리스너에 stopImmediatePropagation을 호출하면 같은 노드의 다음 리스너도 중단되어 child 1만 남음. 실행된 조상의 캡쳐ㅑ 리스너를 되돌리는 것은 아님

| 메서드                   | 취소 | 다른 노드로 전파 중단 | 같은 노드의 다음 리스너 중단          |
| ------------------------ | ---- | --------------------- | ------------------------------------- |
| preventDefault           | 가능 | 없음                  | 없음                                  |
| stopPropagation          | 없음 | 있음                  | 일반적인 같은 노드 다음 리스너느 계속 |
| stopImeediatePropagation | 없음 | 있음                  | 있음                                  |

필요한 동작만 선택
