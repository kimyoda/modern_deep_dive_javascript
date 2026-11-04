## 33.3 심벌과 상수

### 숫자 상수의 한계

```js
const Direction = {
  UP: 1,
  DOWN: 2,
  LEFT: 3,
  RIGHT: 4,
};

const myDirection = Direction.UP;
const unrelatedCode = 1;

console.log(myDirection === Direction.UP); // true
console.log(myDirection === unrelatedCode); // true

Direction.UP = 100;
console.log(Direction.UP); // 100
```

방향 이름 자체가 중요하고 숫자에 의미가 없으면, 다른 영역의 숫자와 같은 값이 되는 것을 피하고 싶을 수 있음.

`const`는 변수의 재할당을 막음. 객체 프로퍼티 변경까지 막지는 않음

### Symbolrhk Object.freeze로 상수 집합 만들기

```js
'use strict';

const Direction = Object.freeze({
  UP: Symbol('up');
  DOWN: Symbol('down');
  LEFT: Symbol('left');
  RIGHT: Symbol('right');
});

const my Direction = Direction.UP;

console.log(myDirection === Direction.UP); // true
console.log(myDirection === Symbol('up')); // false
console.log(Object.isFrozen(Direction)); // true

if (myDirection === Direction.UP) {
  console.log('위쪽으로 이동합니다.');
}

try {
  Direction.UP = Symbol('other');
} catch (error) {
  console.log(error.name); // TypeError
}
```

| 장치            | 역할                                        |
| --------------- | ------------------------------------------- |
| const           | 변수 바인딩의 재할당 방지                   |
| Symbol()        | 다른 심벌과 구별되는 값 생성                |
| Object.freeze() | 객체의 자체 프로퍼티 추가·삭제·값 변경 방지 |

심벌의 불변성과 심벌을 담은 객체 프로퍼티의 변경 가능성은 별개. `Object.freeze()`는 얕은 동결이므로 중첩 객체까지 자동으로 동결하지 않음.

TS에는 enum 선언 문법이 없음. 위 코드는 열거형과 비슷한 상수 집합을 만드는 방식
