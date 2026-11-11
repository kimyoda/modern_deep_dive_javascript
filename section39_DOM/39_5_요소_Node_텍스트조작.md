## 39.5 요소 노드의 텍스트 조작

### 39.5.1 nodeValue

nodeValue는 노드 종류에 따라 의미가 달라짐. Text에서 텍스트를 읽고 바꿀 수 있으나, Element의 nodeValue는 null이다. Attr·Comment 등도 각자의 값을 가짐

```js
const apple = document.getElementById("apple");

console.log(apple.nodeValue); // null
console.log(apple.firstChild.nodeValue); // 'Apple'
apple.firstChild.nodeValue = "사과";
console.log(apple.textContent); // '사과;
```

### 39.5.2 textContent

Element의 textContent는 자손 텍스트를 합친 문자열. 마크업 자체는 포함하지 않음. 값을 대입하면 기존 자식들을 대체하고 텍스트로 처리

```js
const foo = document.getElementById("foo");

console.log(foo.nodeValue); // null
console.log(foo.textContent); // 'Hello world!'
console.log(foo.firstChild.nodeValue); // 'Hello'

foo.textContent = "<span>안녕하세요</span>";
console.log(foo.children.length); // 0
console.log(foo.textContent); // '<span>안녕하세요</span>'
// 화면에도 태그 문자열이 그대로 표시
```

| 구분         | nodeValue      | textContent       | innerText                                 |
| ------------ | -------------- | ----------------- | ----------------------------------------- |
| 주요 용도    | 개별 노드의 값 | DOM 텍스트 내용   | 렌더링된 텍스트                           |
| Element 읽기 | null           | 텍스트            | 스타일·표시 상태 등 반영                  |
| HTML 파싱    | 하지 않음      | 하지 않음         | 하지 않음                                 |
| 고려 사항    | 노드 타입 확인 | 대입 시 자식 교체 | 읽을 때 레이아웃 계산 비용이 생길 수 있음 |

textContent는 일반적인 텍스트 처리에 적합, 사용자가 보는 텍스트가 필요한 경우 innerText가 적합할 수 있음. Document와 DocumentType의 textContent는 null
