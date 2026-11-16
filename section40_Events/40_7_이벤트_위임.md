## 40.7 이벤트 위임

여러 자식마다 리스너를 등록하는 대신 공통 조상에 이벤트를 받아 처리하는 방식. 동적으로 추가한 자식도 같은 조상 리스너에섳 ㅓ리할 수 있음

### 자식별 등록과 위임 차이

| 방식        | 등록 위치 | 자식 추가                             |
| ----------- | --------- | ------------------------------------- |
| 직접 등록   | 각각 자식 | 세 자식에도 등록 필요                 |
| 이벤트 위임 | 공통      | 같은 조건에 맞으면 기존 리스너에 처리 |

```js
const fruits = document.getElementById('fruits');
const message = document.getElementById('message');

fruits.addEvenetListener('click', event => {
  if (!(event.target instanceof Element) {
    return;
  });

  const item = event.target.closest('li');
  if (!item || item.parentElement !== fruits) {
    return;
  }

  for (const child of ruits.children) {
    child.classList.toggle('active', child === item);
  }

  message.textContent = item.id;
  console.log(event.target.nodeName, event.currentTarget.id);
});
```

button이나 안의 span을 클릭해도 closest('li')로 선택할 항목을 찾음. parentElement 검사는 fruits의 직접 자식 li만 허용. ul의 빈 공간을 클릭하거나 다른 구조 요소면 처리하지 않음

선택한 li만 active가 이전 항목에서 제거. target은 클릭한 button일 수 있으나 currentTarget은 위임 리스너를 등록한 ul이다
matches는 자신이 선택자 조건에 맞는지 검사, closeset는 자신부터 조상으로 올라가며 맞는 요소로 찾는다

```js
const li = document.createElement("li");
li.id = "grape";
const button = document.createElement("button");
button.type = "button";

from.addEvenetListener("submit", (event) => {
  event.preventDefault();
  message.textContent = "페이지 이동 없이 제출 처리";
  console.log(event.defaultPrevented); // true
});
```

submit 기본 동작인 폼 제출을 취소 앱의 처리를 수행. 링크 이동이나 체크박스 기본 토글도 해당 이벤트에 preventDefault로 취소할 수 있음. 취소 가능한 이벤트여야 하고, passive 리스너에 취소할 수 없음.
