## 39.4 노드 정보 취득

| 프로퍼티 | 의미                      | 예                   |
| -------- | ------------------------- | -------------------- |
| nodeType | 노드 종류를 나타내는 숫자 | 요소는 1, 텍스트는 3 |
| nodeName | 노드 종류에 따른 이름     | LI, #text, #document |

```js
const apple = document.getElementById("apple");

console.log(apple.nodeType, apple.nodeName); // 1 'LI'
console.log(apple.firstChild.nodeType, apple.firstChild.nodeName); // 3 '#text'
console.log(document.nodeType, document.nodeName); // 9 '#document'
console.log(apple.getAttributeNode("id").nodeName); // 'id'
```

HTML 요소 nodeName은 보통 대문자 태그 이름. XML 등 다른 문서에 대문자라고 일반화하지 않음
