## 39.7 어트리뷰트

### 39.7.1 어트리뷰트 노드와 attributes 프로퍼티

요소 attributes는 Attr 노드들을 담은 live NamedNodeMap. 배열은 아니고 ,이름이나 인덱스로 접근할 수 있음

```js
const input = document.getElementById("user");
const attributes = input.attributes;
const valueAttr = attributes.getNamedItem("value");

console.log(valueAttr.value); // 'Lee'
console.log(valueAttr.ownerElement === input); // true
console.log(valueAttr.parentNode); // null

valueAttr.value = "Kim";
console.log(input.getAttribute("value")); // 'Kim'
```

attributes 프로퍼티체에 새로운 맵을 대입할 수 없다는 것과 맵 내부의 어트리뷰트를 바꿀 수 없다는 것은 다름
Attr.value나 어트리뷰트 조작 API로 내용을 변경할 수 있음

### 39.7.2 HTML 어트리뷰트 조작

| 메서드                    | 용도               | 주요 반환값      |
| ------------------------- | ------------------ | ---------------- |
| getAttribute(name)        | 어트리뷰트 값 읽기 | 문자열 또는 null |
| setAttribute(name, value) | 추가·변경          | undefined        |
| hasAttribute(name)        | 확인               | boolean          |
| removeAttribute(name)     | 제거               | undefined        |

```js
const input = document.getElementById("user");

console.log(input.getAttribute("value")); // 'Lee'
console.log(input.getAttribute("missing")); // null

input.setAttribute("data-status", "ready");
console.log(input.hasAttribute("data-status")); // true

input.removeAttribute("data-status");
console.log(input.hasAttribute("data-status")); // false
```

### 39.7.3 HTML 어트리뷰트와 DOM 프로퍼티

HTML 어트리뷰트는 마크업에 표현되는 속성, DOM 프로퍼티는 객체에서 접근하는 속성. 서로 반영되는 경우가 많지만 이름·타입·역할이 항상 같지 않음

| HTML 어트리뷰트      | 관련 DOM 프로퍼티     | 특징                            |
| -------------------- | --------------------- | ------------------------------- |
| id                   | id                    | 문자열로 반영                   |
| class                | className, classList  | 전체 문자열 / 토큰 목록         |
| for                  | htmlFor               | label 연결 대상                 |
| maxlength            | maxLength             | 숫자 프로퍼티                   |
| colspan              | colSpan               | td·th의 숫자 프로퍼티           |
| value                | defaultValue, checked | 텍스트 input의 기본값 / 현재 값 |
| disabled             | disabled              | 존재 기반 boolean               |
| 대응 어트리뷰트 없음 | textContent           | 노드 텍스트 내용                |

colspan에 대응하는 DOM 프로퍼티는 **colSpan.** 어트리뷰트가 항상 고정된 최초 값이라는 설명도 정확하지 않다
setAttribute 등으로 바꿀 수 있음

#### 텍스트 input의 초기 상태와 현재 상태

```js
const input document.createElement('input');
input.type = 'text';
input.setAttribute('value', 'Lee');

console.log(input.getAttribute('value')); // 'Lee'
console.log(input.defaultValue); // 'Lee'
console.log(input.value); // 'Lee'

// 현재 값을 변경
input.value = 'Park';
console.log(input.getAttribute('value')); // 'Lee'
console.log(input.defaultValue); // 'Lee'
console.log(input.value); // 'Park'
```

| 단계                      | value 어트리뷰트 | defaultValue | value |
| ------------------------- | ---------------- | ------------ | ----- |
| value="Lee" 설정          | Lee              | Lee          | Lee   |
| 현재 값을 Park로 변경     | Lee              | Lee          | Park  |
| 어트리뷰트를 Kim으로 변경 | Kim              | Kim          | Park  |

텍스트 input은 현재 값이 아직 변경되지 않았으면 value 어트리뷰트 변경이 현재 값에도 반영될 수 있음. 사용자 입력이나 value 대입 이후에 현재 값이 별도로 관리. 폼을 reset하면 기본값으로 돌아가는 동작도 구분과 관련.

```mermaid
flowchart TD
  A["value 어트리뷰트] <-->|"반영"| D["defaultValue"]
  A -->|"아직 현재 값이 변경되지 않았다면 동기화"| V["value: 현재 입력값"]
  U["사용자 입력·value 대입"] --> V
  R["폼 reset"] -->|"기본값으로 복원"| V
  D -.->|"reset에서 사용할 기본값"| R
```

JS에서 value에 대입하는 것만으로 input 이벤트가 자동 발생하지 않음. 다음과 같이 이벤트에서 현재 값을 읽는다

```js
const input = document.getElementById("user");
input.addEventListener("input", (event) => {
  console.log(event.target.value);
});
```

#### boolean 어트리뷰트

boolean 어트리뷰트는 문자열 의미가 아닌 **존재 여부**로 참·거짓을 나타낸다

```js
const checkbox = document.createElement("input");
checkbox.type = "checkbox";
checkbox.setAttribute("checked", "false");

console.log(checkbox.hasAttribute("checked")); // true
console.log(checkbox.defaultChecked); // true
console.log(checkbox.checked); // true

checkbox.checked = false;
console.log(checkbox.checked); // false: 현재 상태
console.log(checkbox.defaultChecked); // true: 기본 상태
```

기본 checked 상태를 없애면 removeAttribute('checked') 또는 defaultChecked = false를 사용. 현재 상태는 checked로 다룬다

### 39.7.4 data 어트리뷰트와 dataset

data-\*는 요소에 사용자 정의 데이터를 저장하는 표준 방식. datasetdms DOMStringMap으로 제공

```js
const li = document.getElementById("apple");
li.setAttribute("data-user-id", "123");

console.log(li.dataset.userId); // '123'

li.dataset.role = "admin";
console.log(li.getAttribute("data-role")); // 'admin'

li.dataset.count = 3;
console.log(li.dataset.count); // '3': 문자열

delete li.dataset.role;
console.log(li.hasAttribute("data-role")); // false
console.log(li.dataset.role); // undefined
```

| HTML 이름       | dataset 접근      |
| --------------- | ----------------- |
| data-user-id    | dataset.userId    |
| data-role       | dataset.role      |
| data-item-count | dataset.itemCount |

사용자가 DOM과 dataset을 수정할 수 있어 data-role을 서버 권한 판단의 근거로 사용하지 않음.
