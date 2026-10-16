## 20.6 strict mode 적용에 의한 변화

### 20.6.1 일반 함수의 this

strict mode에서 함수를 수신 객체 없이 일반 함수로 호출하면 `this`에 `undefined`가 바인딩된다.

**예제 20-12**

```js
(function () {
  "use strict";

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo {} (새로 생성한 인스턴스, 표시 형식은 환경마다 다름)
  }
  new Foo();
})();
```

### 20.6.2 arguments 객체

strict mode에서는 단순 매개변수에 새로운 값을 대입해도 `arguments` 객체의 대응 요소에 변경이 반영되지 않는다.

**예제 20-13**

```js
(function (a) {
  "use strict";
  // 매개변수에 전달된 인수를 재할당
  a = 2;

  // 변경된 인수가 arguments객체에 반영되지 않음
  console.log(arguments); // 인수 1이 그대로 유지됨 (출력 표현은 환경마다 다름)
})(1);
```

### arguments 연결을 값으로 확인

```js
function sloppy(a) {
  a = 2;
  return arguments[0];
}

function strict(a) {
  "use strict";
  a = 2;
  return arguments[0];
}

console.log(sloppy(1)); // 2: 비엄격 일반 스크립트 또는 CommonJS 기준
console.log(strict(1)); // 1
```

ES 모듈로 옮기면 `sloppy` 함수에도 strict mode가 적용되므로 첫 결과가 `1`이 된다. 기본값·나머지·구조 분해처럼 단순하지 않은 매개변수 목록에서도 `arguments`와의 연결 규칙이 달라진다. 위 비교는 **단순 매개변수 목록**을 기준으로 한다.

## 복습 질문과 답변

1. **strict mode와 ESLint는 같은가?**

   아니다. strict mode는 JavaScript 언어 자체의 문법 및 실행 규칙이고, ESLint는 코드를 실행하지 않고 검사하는 정적 분석 도구다.

2. **`"use strict";`는 어디에 작성하는가?**

   스크립트 또는 함수 몸체의 **지시문 영역(Directive Prologue)**에 작성한다. 보통 가장 앞에 둔다.

3. **코드 중간에 `"use strict";` 문자열을 작성하면 strict mode가 되는가?**

   아니다. 다른 실행문 뒤에 놓인 문자열 표현식은 strict mode 지시문으로 인식되지 않는다.

4. **strict mode에서 일반 함수 호출 시 `this`는 무엇인가?**

   수신 객체 없이 일반 함수로 호출하면 `undefined`다. 메서드 호출과 `new` 호출은 각 호출 방식의 `this` 바인딩 규칙을 따른다.

5. **ES 모듈에서도 `"use strict";`가 필요한가?**

   아니다. ES 모듈 코드는 자동으로 strict mode가 적용된다.

6. **`SyntaxError`가 발생하는 코드 앞의 `console.log`는 실행되는가?**

   같은 스크립트나 함수가 문법 분석 단계에서 실패하면 실행 자체가 시작되지 않는다. 따라서 앞에 작성된 `console.log`도 실행되지 않는다.

7. **strict mode에서 매개변수 값을 변경하면 `arguments[0]`에도 반영되는가?**

   아니다. strict mode에서는 매개변수 값의 재할당이 `arguments[0]`에 반영되지 않는다.

8. **`delete x`와 `delete obj.x`는 같은가?**

   아니다. `delete x`는 식별자 자체를 삭제하려는 구문이므로 strict mode에서 `SyntaxError`가 발생한다. `delete obj.x`는 객체의 프로퍼티를 삭제하는 연산이다.
