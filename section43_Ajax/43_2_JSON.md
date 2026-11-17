## 43.2 JSON

JSON은 언어에 종속되지 않은 텍스트 데이터 형식. **JavaScript 객체와 JSON 문자열은 구분**

### 43.2.1 JSON 표기

```json
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"],
  "nickname": null
}
```

객체의 키와 문자열은 큰 따옴표로 감싼다. 값으로 문자열·숫자·불리언·null·배열·객체를 쓸 수 있음. 일반 JSON에 주석, 후행 쉼표, `undefined`, 함수가 없음.
최상위 값은 객체나 배열 이외의 유효한 JSON 값도 가능

### 43.2.2 JSON.stringify: 직렬화

```js
const user = { name: "Lee", age: 20, alive: true };

console.log(JSON.stringify(user));
// {"name": "Lee", "age":20, "alive":true }

console.log(JSON.stringify(user, null, 2)); // 2칸 들여쓰기

const withoutNumbers = JSON.stringify(
  user,
  (key, value) => {
    if (typeof value === "number") {
      return undefined;
    }
    return value;
  },
  2,
);

console.log(withoutNumbers); // name, alive만 남는

const todos = [{ id: 1, content: "HTML", completed: true }];
console.log(typeof JSON.stringify(todos)); // string
```

두 번째 인수인 replacer는 포함할 값을 바꿀 수 있고, 세 번째 인수는 들여쓰기를 지정. 객체 프로퍼티에 빠지는 값은 배열 안에서 `null`로 변환되는 경우가 있음. `JSON.stringify([undefined])`는 `'[null]'` 이다

### 43.2.3 JSON.prse: 역직렬화

```js
const object = JSON.parse('{"content":"HTML", "completed":true}');
const list = JSON.parse('[{"id":1, "content": "HTML"}]');

console.log(object.content); // HTML
console.log(Array.isArray(list)); // true
console.log(listt[0].id); // 1

try {
  JSON.parse('{"id":1,}');
} catch (error) {
  console.log(error.name); // SyntaxError
}
```

`JSON.parse`는 JSON 텍스트를 JavaScript 값으로 바꿈. 배열을 파싱하면 배열이 반환, 빈 응답은 그대로 파싱할 수 없음. 파싱 성공은 데이터 구조까지 검증했다는 뜻은 아님.
