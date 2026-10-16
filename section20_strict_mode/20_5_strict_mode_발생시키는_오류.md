## 20.5 strict mode가 발생시키는 오류

### 20.5.1 암묵적 전역

strict mode에서 선언되지 않은 식별자에 대입하면 `ReferenceError`가 발생한다. 존재하지 않는 식별자를 읽을 때에는 strict mode 여부와 관계없이 `ReferenceError`가 발생한다.

**예제 20-08**

```js
(function () {
  "use strict";

  x = 1; // ReferenceError: x is not defined
  console.log(x); // 위에서 오류가 발생하므로 실행되지 않음
})();
```

### 20.5.2 변수, 함수, 매개변수의 삭제

strict mode에서 `delete` 연산자로 **변수, 함수 또는 매개변수 식별자 자체**를 삭제하려 하면 `SyntaxError`가 발생한다. 객체의 프로퍼티를 삭제하는 `delete obj.key`와는 다르다.

**예제 20-09**

```js
(function () {
  "use strict";

  var x = 1;
  delete x; // SyntaxError: 식별자 직접 삭제 금지

  function foo(a) {
    delete a;
  }
  delete foo;
})();
```

> **주의:** 예제 20-09는 문법 오류를 설명하기 위해 일부러 잘못 작성한 코드다. `delete x`, `delete a`, `delete foo`는 각각 strict mode에서 허용되지 않는다. 파일 전체가 문법 분석에 실패하므로 실행은 시작되지 않는다.

### 20.5.3 매개변수 이름의 중복

strict mode에서 같은 함수의 매개변수 이름을 중복해서 선언하면 `SyntaxError`가 발생한다.

**예제 20-10**

```js
(function () {
  "use strict";

  // SyntaxError: strict mode에서 매개변수 이름 중복
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

> **주의:** 예제 20-10도 문법 오류를 확인하기 위한 코드다. 함수 호출 이전에 오류가 발생하므로 `foo(1, 2)`는 실행되지 않는다.

### 20.5.4 with문 사용

`with` 문을 strict mode에서 사용하면 `SyntaxError`가 발생한다. `with` 문은 전달받은 객체를 식별자 검색 환경에 추가한다. 같은 객체의 프로퍼티를 반복해 사용할 때 객체 이름을 생략할 수 있지만, 코드의 의미를 이해하기 어려워지고 최적화에도 불리하므로 사용하지 않는 것이 좋다.

```js
(function () {
  "use strict";

  // SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

> **주의:** `with` 문 예제도 의도적으로 문법 오류가 발생한다. 원본에 `20-11` 예제 번호가 따로 적혀 있지 않아 임의로 번호를 붙이지 않았다.
