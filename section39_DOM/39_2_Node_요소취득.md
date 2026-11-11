## 39.2 요소 노드 취득

### 39.2.1 id를 이용한 취득

`getElementById(id)`는 해당 id를 가진 첫 요소를 반환, 없으면 `null`을 반환. id는 문서 안에서 고유하게 작성

```js
const apple = document.getElementById("apple");
console.log(apple.textContent); // 'Apple'
console.log(document.getElementById("missing")); // null

if (apple) {
  apple.classList.add("active");
}
```

DocumentFragment에서 getElementById를 사용할 수 있음. 일반 Element에 메서드가 없어 요소 내부 검색에 querySelector 등을 사용

일부 id가 `window` 이름 있는 프로퍼티로 노출되는 레거시 동작에 의존하지 않음. `apple.style`처럼 선언 없이 접근하기보다 명시적으로 요소를 취득

### 39.2.2 태그 이름을 이용한 취득

```js
const fruits = document.getElementById('fruits');
const items = fruits.getElementsByTagNames('li');

console.log(items.length); // 3
console.log(items[0].textContent); // 'Apple'
console.log(fruits.getElementsByTagName(*).length); // 3
```

반환값 live HTMLCollection. Document에 호출하면 문서 전체, Element에 호출하면 요소의 자손을 검색하며 호출한 요소 자신은 포함하지 않음

### 39.2.3 class를 이용한 취득

```js
const redItems = document.getElementsByClassName("red");
console.log(redItems.length); // 3

// red, active 클래스를 모두 가진 요소를 검색
const selected = document.getElementsByClassName("red active");
console.log(selected.length); // HTML 초기 상태에 0
```

반환값 live HTMLCollection. 결과가 없으면 0인 컬렉션을 반환

### 39.2.4 CSS 선택자를 이용한 취득

| 선택자       | 의미                    | 예                 |
| ------------ | ----------------------- | ------------------ |
| \*           | 모든 요소               | \*                 |
| 태그         | 해당 태그 요소          | li                 |
| #id          | id 일치                 | #apple             |
| .class       | 클래스 토큰 포함        | .red               |
| [attr=value] | 어트리뷰트 조건         | input[type="text"] |
| A B          | A의 자손 B              | #fruits li         |
| A > B        | A의 직접 자식 B         | #fruits > li       |
| A + B        | A 바로 다음 형제 요소 B | li + li            |
| A ~ B        | A 뒤에 있는 형제 요소 B | li ~ li            |
| :상태        | 의사 클래스             | input:checked      |
| ::부분       | 의사 요소               | .box::before       |

의사 요소는 실제 Element 노드가 아니므로 querySelector로 `::before`에 해당하는 요소 객체를 얻을 수 없음

```js
const fruits = document.getElementById("fruits");
const first = fruits.querySelector(".red");
const all = fruits.querySelector(":scope > li");

console.log(first.id); // 'apple'
console.log(all.length); // 3
console.log(fruits.querySelector(".missing")); // null
console.log(fruits.querySelectorAll(".missing").length); // 0
```

- querySelector: 첫 요소 또는 null
- querySelectorAll: 선택 시점 일치 요소를 담은 static NodeList
- 문법이 잘못된 선택자: 이름이 SyntaxError인 DOMException
- 동적인 id를 CSS 선택자로 조합할 때 CSS.escape를 사용. id 자체로 찾을 목적이면 getElementById가 간단

```js
const id = "item:1";
const element = document.createElement("div");
element.id = id;
document.body.appendChild(element);

console.log(document.querySelector(`#${CSS.escape(id)}`) === element); // true
element.remove();
```

### 39.2.5 특정 선택자와 일치하는지 확인

`matches`는 해당 요소 자신이 선택자 조건에 맞는지 검사.

```js
const apple = document.getElementById("apple");
console.log(apple.matches("#fruits > li.red")); // true
console.log(apple.matches(".blue")); // false
```

### 39.2.6 HTMLCollection과 NodeList

둘 다 배열처럼 인덱스와 length를 제공, Array는 아님. Array.from이나 스프레드 문법으로 배열로 변환할 수 있음

| 방법                   | 반환 타입      | DOM 변경 반영 |
| ---------------------- | -------------- | ------------- |
| getElementsByTagName   | HTMLCollection | live          |
| getElementsByClassName | HTMLCollection | live          |
| children               | HTMLCollection | live          |
| querySelectorAll       | NodeList       | static        |
| childNodes             | NodeList       | live          |

NodeList는 이름만으로 live/static을 판단하면 안됨. API가 반환됐는지 확인. NodeList에 forEach가 있으나 HTMLCollection에 없으므로 변환해서 순회하는 방법이 편리

### live 컬렉션에서 요소가 건너뛰어지는 이유

```js
const redItems = document.getElementsByClassName("red");

for (let i = 0; i < redItems.length; i++) {
  redItems[i].className = "blue";
}
```

| 시점              |   i | redItems 목록         | 결과                                 |
| ----------------- | --: | --------------------- | ------------------------------------ |
| 시작              |   0 | Apple, Banana, Orange | 세 요소 모두 red                     |
| 첫 변경 직후      |   0 | Banana, Orange        | Apple이 blue가 되어 검색 결과에 빠짐 |
| 다음 반복         |   1 | Banana, Orange        | 인덱스 1인 Orange를 blue로 변경      |
| 두 번째 변경 직후 |   1 | Banana                | 길이가 1로 줄어듦                    |
| 종료 검사         |   2 | Banana                | 2 < 1이 false므로 종료               |

Apple과 Orange가 blue가 되고 Banana는 red로 남음. `.red` 조건의 결과 목록에서 제외된 것

**해결 방법 1: 배열로 복사한 목록을 순회.**

```js
const redItems = document.getElementsByClassName("red");

Array.from(redItems).forEach((element) => {
  element.classList.replace("red", "blue");
});
```

**해결 방법 2: static 목록을 사용.**

```js
document.querySelectorAll(".red").forEach((element) => {
  element.classList.replace("red", "blue");
});
```

**해결 방법 3: live 목록이 줄어드는 특성을 이용.**

```js
const redItems = document.getElemensByClassName('red');

whiel (redItems.length > 0) {
  redItems[0].classList.replaced('red', 'blue');
}
```

뒤에서 부터 순회하는 방법도 가능. 처리 코드가 컬렉션을 어떻게 바꾸는지 명확할 때 사용

static 목록이나 배열 복사는 **목록 구성만 고정**한다. 내부 노드 객체를 동결하거나 깊게 복제하지 않음
