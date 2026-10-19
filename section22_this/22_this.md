# 22장 this

## 학습 목표 및 체크리스트

- **일반 함수의 `this`는 정의 위치가 아니라 호출 방식에 따라 결정된다.**
- 메서드를 다른 객체에 할당하거나 변수로 분리했을 때 비교
- `new`, `call`, `apply`, `bind`의 차이를 확인
- 화살표 함수는 자신만의 this를 만들지 않고 바깥 this를 사용한다
- 타이머, 이벤트 같은 콜백 API의 호출 규칙도 확인

| 호출                 | 일반 함수의 this                      |
| -------------------- | ------------------------------------- |
| `fn()`               | sloppy mode: 전역 객체 / strict mode: undefined |
| `obj.fn()`           | 수신 객체 obj                         |
| `new Fn()`           | 생성 중인 인스턴스                    |
| `fn.call(obj, a)`    | 전달한 obj                            |
| `fn.apply(obj, [a])` | 전달한 obj                            |
| `fn.bind(obj)()`     | 바인딩한 obj                          |

이 표는 일반 함수를 기준으로 한다. 화살표 함수는 `call`, `apply`, `bind`로 `this`를 변경할 수 없다. 바인딩된 생성자 함수를 `new`로 호출하면 생성자 호출 규칙이 적용되어 바인딩한 `this`는 무시된다.

> **실행 환경 주의:** `window`, `window.name`, 전역 `var`를 사용하는 예제는 브라우저의 일반 `<script>` 환경을 기준으로 한다. strict mode에서 일반 함수의 `this`는 `undefined`이고, Node.js의 `setTimeout`은 일반 함수 콜백에 `Timeout` 객체를 `this`로 제공한다. 모든 콜백에 동일한 규칙이 적용되는 것은 아니다.

## 22.1 this 키워드

객체는 상태를 나타내는 프로퍼티와 동작을 나타내는 메서드를 하나로 묶은 복합적인 자료구조다.
메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 해당 객체에 접근할 방법이 필요하다.

**예제 22-01**

```js
const circle = {
  // 프로퍼티: 객체 고유의 상태 데이터
  radius: 5,
  // 메서드: 상태 데이터를 참조하고 조작하는 동작
  getDiameter() {
    // 메서드가 객체의 프로퍼티나 다른 메서드를 참조하려면
    // 여기에서는 객체 이름 circle을 직접 사용한다
    return 2 * circle.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

객체 이름으로 자기 자신을 직접 참조하는 방식은 재사용성과 유연성 면에서 바람직하지 않을 수 있다.

**예제 22-02** (개념을 설명하는 의사 코드이며 실행 불가)

```text
function Circle(radius) {
 // 아직 생성될 인스턴스를 가리키는 변수 이름을 알 수 없다
 ????.radius = radius;
}

Circle.prototype.getDiameter = function () {
 // 아직 생성될 인스턴스를 가리키는 변수 이름을 알 수 없다
 return 2 * ????.radius;
};

// 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 한다
const circle = new Circle(5);
```

생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수가 정의되어 있어야 한다. 생성자 함수가 호출되는 시점에는 앞으로 생성할 인스턴스의 이름을 미리 알 수 없다. 자바스크립트는 이런 상황에서 사용할 수 있는 특별한 식별자 `this`를 제공한다.

**`this`는 호출 방식에 따라 결정되는 대상을 참조하는 자기 참조 변수다. 메서드 호출에서는 일반적으로 메서드를 호출한 객체를, `new`를 사용한 생성자 호출에서는 생성 중인 인스턴스를 가리킨다.**

`this` 바인딩은 자바스크립트 엔진이 호출 문맥에 따라 결정한다. 일반 함수의 `this` 바인딩은 함수가 정의된 위치가 아니라 호출 방식에 따라 동적으로 결정된다. 단, 화살표 함수는 바깥쪽의 `this`를 그대로 사용한다.

### 참고: `this` 바인딩

바인딩이란 식별자와 값을 연결하는 과정을 말한다. `this` 바인딩은 현재 실행 문맥에서 `this`가 참조할 값을 결정하는 것이다.

**예제 22-03**

```js
// 객체 리터럴
const circle = {
  radius: 5,
  getDiameter() {
    // this는 메서드를 호출한 객체를 가리킨다
    return 2 * this.radius;
  },
};

console.log(circle.getDiameter()); // 10
```

이 예제에서 `this`는 `getDiameter()`를 호출한 `circle`을 가리킨다.

**예제 22-04**

```js
// 생성자 함수
function Circle(radius) {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다
  return 2 * this.radius;
};

// 인스턴스 생성
const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

생성자 함수 안에서 `this`는 `new`로 생성 중인 인스턴스를 가리킨다. `getDiameter`를 인스턴스의 메서드로 호출할 때도 `this`는 그 인스턴스를 가리킨다.

**일반 함수의 `this`는 호출 방식에 따라 동적으로 결정된다.**

strict mode도 일반 함수의 `this` 바인딩에 영향을 준다. 다음 예제는 **브라우저의 일반 스크립트(sloppy mode)** 기준이다.

**예제 22-05** (브라우저 일반 스크립트 기준)

```js
// this는 어디서든지 참조 가능하다
// 브라우저 일반 스크립트의 최상위 this는 전역 객체 window를 가리킨다
console.log(this); // window

function square(number) {
  // sloppy mode에서 일반 함수의 this는 전역 객체
  console.log(this); // window
  return number * number;
}
square(2);

const person = {
  name: "Lee",
  getName() {
    // 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다
    console.log(this); // {name: "Lee", getName: f}
    return this.name;
  }
};
console.log(person.getName()); // Lee

function Person(name) {
  this.name = name;
  // 생성자 함수 내부의 this는 new로 생성 중인 인스턴스를 가리킨다
  console.log(this); // Person {name: "Lee"}
}

const me = new Person("Lee");
```

`this`는 특히 메서드와 생성자 함수에서 유용하다. 일반 함수 호출에서는 strict mode 여부에 따라 전역 객체 또는 `undefined`가 될 수 있다.
