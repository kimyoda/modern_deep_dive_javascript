## 20.3 전역에 strict mode를 적용하는 것은 피하자

일반적인 브라우저 `<script>` 태그에 작성한 strict mode 지시문은 **해당 스크립트에만** 적용된다.

**예제 20-05**

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      "use strict";
    </script>
    <script>
      x = 1; // 비엄격 모드: 암묵적 전역 생성
      console.log(x); // 1
    </script>
    <script>
      "use strict";

      y = 1; // ReferenceError: y is not defined
      console.log(y); // 위에서 오류가 발생하므로 실행되지 않음
    </script>
  </body>
</html>
```

strict mode는 다른 일반 스크립트에 영향을 주지 않고, 해당 `<script>` 내부에만 적용된다.

strict mode 스크립트와 비엄격 모드 스크립트를 혼용하면 예기치 않은 오류가 발생할 수 있다. 기존 일반 스크립트를 사용할 때는 즉시 실행 함수(IIFE)로 코드를 감싸 스코프를 분리하고, 함수 몸체의 시작 부분에 strict mode를 적용하는 방법을 사용할 수 있다.

**예제 20-06**

```js
// 즉시 실행 함수의 시작 부분에 strict mode 적용
(function () {
  "use strict";

  // strict mode로 실행할 코드를 작성한다.
})();
```

> **현재 권장 방식:** 새 JavaScript 프로젝트에서는 가능한 경우 ES 모듈을 사용하면 별도의 `"use strict";` 지시문 없이 strict mode가 자동 적용된다.
