## 48.3 ES6 모듈 - ESM

```html
<script type="module" src="./app.mjs"></script>
```

`type="module"`이 해당 파일을 모듈로 로드하도록 지정. 브라우저에 `.js`도 사용할 수 있고, `.mjs`는 모듈임을 이름으로 구분하는 방법. 서버는 자바스크립트 MIME 타입으로 응답해야 한다

ESM에 strict mode가 자동 적용. 일반적인 모듈 스크립트는 HTML 파싱을 막지 않고 의존성을 로드한 뒤 실행.

### 48.3.1 모듈 스코프

일반 script에 `var`로 선언한 같은 이름은 충돌할 수 있음. 아래 두 파일을 이 순서로 로드했다고 가정

```js
// classic-foo.js
var x = "foo";
console.log(window.x); // foo
```

```js
// classic-bar.js
var x = "bar";
console.log(window.x); // bar
```

ESM 최상위 선언은 파일별 모듈 스코프에 속함

```js
// foo.mjs
var x = "foo";
console.log(x); // foo
console.log(window.x); // 새 페이지가 다른 전역 x가 없으면 undefined
```

```js
// bar.mjs
var x = "bar";
console.log(x); // bar: foo.mjs의 x와 다른 변수
```

일반 script의 최상위 `let`·`const`는 `window` 프로퍼티가 되지는 않지만 전역 렉시컬 환경을 공유. `var`을 `let`으로 바꾸는 것만으로 파일별 스코프가 생기지 않는다

### 48.3.2 export 키워드

**lib.mjs**

```js
const pi = Math.PI;

function square(value) {
  return value * value;
}

class Person {
  constructor(name) {
    this.name = name;
  }
}

export { pi, square, Person };
```

각 선언 앞에 `export`를 붙여도 된다. `export { pi, square, Person }`의 중괄호는 **내보낼 이름의 목록**이다. 이문법 자체가 세 값을 담는 객체를 생성하는 것은 아님

### 48.3.3 import 키워드

**app.mjs**

```js
import { pi, square, Person } from "./lib.mjs";

console.log(pi); // 3.14159265389793
console.log(square(10)); // 100
console.log(new Person("Lee")); // Person { name: 'Lee' }
```

**index.html**

```html
<!doctype html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <title>ESM 실습</title>
  </head>
  <body>
    <script type="module" src="./app.mjs"></script>
  </body>
</html>
```

진입점인 `app.mjs`만 HTML에서 지정. `lib.mjs`는 `app.mjs`의 import를 따라 로드. 상대 경로는 가져오는 모듈 파일을 기준으로 한다. 보통 서버는 `./lib`를 `./lib.mjs`로 자동 보완하지 않으므로 파일 이름과 확장자까지 적는다.

#### namespace import와 별칭

아래는 `app.mjs`를 대체해 각각 실행하는 방법

```js
import * as lib from "./lib.mjs";

console.log(lib.pi);
console.log(lib.square(10));
console.log(new lib.Person("Lee"));
```

```js
import { pi as PI, square as sq, Person as P } from "./lib.mjs";

console.log(PI);
console.log(sq(2)); // 4
console.log(new P("Kim")); // Person { name: 'Kim' }
```

`import * as lib`의 `lib`는 모듈 namespace 객체. import는 내보낸 값과 연결되는 **라이브 바인딩**이며, 가져온 변수에 새 값을 재할당할 수 없음. 가져온 객체 자체의 속성이 변경 가능한지 그 객체의 설계에 달려 있음

#### default export

한 모듈에 default export를 하나만 둘 수 있고, named export와 함 께 둘 수 있음. default import는 중괄호 없이 원하는 이름을 붙임

```js
// square.mjs
const square = (value) => value * value;
export default square;
```

```js
// app.mjs: 앞선 예제 대체
import calculateSqaure from "./square.mjs";
console.log(calculateSquare(3)); // 9
```

`export default const square = ...`는 문법 오류. 위처럼 변수를 먼저 선언 `export default function square(value) { return value * value; }`로 공개할 수 있음

## 질문 및 답변

### 1. 일반 script 파일을 나누면 모듈 스코프가 생기는 지

- 아님. ESM이나 다른 모듈 시스템 처리가 필요

### 2. ESM 파일은 반드시 `.mjs`여야 하는 지

- 아님. 브라우저는 `type="module"`과 올바른 응답 타입으로 `.js`도 처리

### 3. `export { a, b }`는 객체를 만드는 문법인지

- 아님. 공개할 이름 목록

### 4. named import, default import 차이는

- named는 공개 이름을 지정, default는 중괄호 없이 원하는 이름으로 받음

### 5. Node.js는 CommonJS만 지원하는지

- 아님. `.mjs`는 ESM, `.cjs`는 CommonJS이고 `.js`는 패키지 설정 등에 따라 해석된다

[MDN — JavaScript 모듈](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Modules),
[Node.js — ESM](https://nodejs.org/api/esm.html)
