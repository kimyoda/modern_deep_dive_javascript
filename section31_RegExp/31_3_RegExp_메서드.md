## 31.3 RegExp 메서드

| 메서드             | 값                                      |
| ------------------ | --------------------------------------- |
| regexp.exec(text)  | 한 번의 일치 결과 배열 또는 null        |
| regexp.test(text)  | 불리언                                  |
| text.match(regexp) | 배열 또는 null, g 여부에 따라 형태 차이 |

### 31.3.1 exec

한 번의 호출에서 한 매칭 결과를 반환한다. 결과의 0번 요소는 전체 매칭 문자열, 캡처 그룹은 뒤에 추가

```js
const match = /is/i.exec("Is this all there is?");

console.log(match[0]); // Is
console.log(match.index); // 0
console.log(/xyz/.exec("hello")); // null
```

g 플래그에서 반복 호출로 다음 결과를 찾을 수 있음

```js
const pattern = /\d+/g;
const tesxt = "A12 B34";
const found = [];
let match;

while ((match = pattern.exec(text)) !== null) {
  found.push([match[0], match.index]);
}

console.log(found); // [['12', 1], ['34', 5]]
console.log(pattern.lastIndex); // 0: 검색 실패 후 초기화
```

반복문은 빈 문자열과 매칭되지 않는 패턴. 빈 문자열도 매칭하는 패턴을 exec 반복문에 사용하면 위치를 진행시키는 별도 처리가 필요할 수 있음

### 31.3.2 test

```js
console.log(/is/i.test('Is this all there is?')); // true
console.log(/xyz/test('hello')); // false
```

### 31.3.3 match

```js
const text = "Is this all there is?";

console.log(text.match(/is/)[0]); // is
console.log(text.match(/is/).index); // 5
console.log(text.match(/is/g)); // ['is', 'is']
console.log(text.match(/is/gi)); // ['Is', 'is', 'is']
console.log(text.match(/is/g)); // null
```

| 호출          | 결과 특징                      |
| ------------- | ------------------------------ |
| match, g 없음 | 첫 결과 캡처·index 등          |
| match, g 있음 | 전체 일치 문자열들을 모은 배열 |
| 일치 없음     | 빈 배열이 아니라 null          |

전체 검색 결과가 없을 때 배열이 필요하면 `text.match(pattern) ?? []`처럼 처리할 수 있음
