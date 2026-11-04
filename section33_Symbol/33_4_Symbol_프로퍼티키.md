## 33.4 심벌과 프로퍼티 키

계산된 프로퍼티 이름과 대괄호 접근을 사용

```js
const id = Symbol('id');

const user = {
  id: '문자열 키의 값',
  [id]: 100,
};

console.log(user.id); // '문자열 키의 값'
console.log(user.[id]); // 100
console.log(user[Symbol('id')]); // undefined

const shared = {
  [Symbol.for('study.userId')]: 200,
};

console.log(shared[Symbol.for('study.userId')]); // 200
```

`user.id`는 문자열 키 `'id'`를 읽음. 심벌 변수 `id`를 키로 사용하면 `user[id]`로 접근.

서로 다른 코드가 **새로운 Symbol()을 각각 생성**하면 키가 겹치지 않음. 같은 심벌을 재사용하거나 같은 키의 `Symbol.for()`를 사용하면 같은 프로퍼티를 가리킴

```js
const firstkey = Symbol.for("shared");
const secondkey = Symbol.for("shared");
const object = { [firstKey]: 1 };

object[secondKey] = 2;

console.log(firstKey === secondKey); // true
console.log(object[firstKey]); // 2
```
