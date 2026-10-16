## 20.2 strict mode의 적용

스크립트의 지시문 영역(보통 맨 앞)에 `"use strict";`를 작성하면 strict mode가 적용된다.

**예제 20-02**

```js
"use strict";

function foo() {
  x = 10; // ReferenceError
}
foo();
```

함수 몸체의 지시문 영역에 `"use strict";`를 작성하면 해당 함수와 중첩 함수에도 strict mode가 적용된다.
**예제 20-03**

```js
function foo() {
  "use strict";

  x = 10; // ReferenceError
}
foo();
```

`"use strict";`가 일반 실행문 뒤에 놓이거나 괄호로 감싸진 문자열 표현식이 되면 strict mode 지시문으로 인식되지 않는다. (다른 문자열 지시문이 먼저 오는 것은 가능하다.)

**예제 20-04**

```js
function foo() {
  x = 10; // 비엄격 모드에서는 암묵적 전역이 생성됨
  ("use strict"); // 지시문이 아닌 일반 문자열 표현식
}
foo();
```

> **주의:** 예제 20-02와 20-03은 선언되지 않은 변수에 대입하므로 실행 중 `ReferenceError`가 발생하는 것이 정상이다. 예제 20-04는 별도의 비엄격 환경에서 실행해야 한다.
