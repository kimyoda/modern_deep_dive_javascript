# 49장 Babel과 Webpack을 이용한 ES6+ 개발 환경 구축

> **Babel은 문법을 변환, Webpack은 의존하는 모듈을 묶음. 실행 환경에 없는 표준 기능은 폴리필로 보완**

## 개발 도구의 역할

| 도구·설정           | 역할                                    | 예                                        |
| ------------------- | --------------------------------------- | ----------------------------------------- |
| Babel               | 대상 환경에 실행할 수 있도록 문법 변환  | 화살표 함수, 지수 연산자, 클래스 필드     |
| `@babel/preset-env` | 지원 환경에 따라 필요한 변환 선택       | `targets`에 맞는 플러그인 적용            |
| `babel-loader`      | Webpack 처리 과정에 Babel 연결          | 소스 파일을 Babel로 변환                  |
| Webpack             | import를 따라 모듈을 분석하고 번들 생성 | `main.js`와 `lib.js`를 `bundle.js`로 묶음 |
| `core-js`           | 없는 ECMAScript 기능을 실행 시 보완     | `Promise`, `Object.assign`, `Array.from`  |

### 브라우저 ES6 지원 현황

지원할 브라우저에 어떤 기능이 필요한지 확인

| 대상           | 내용                                     |
| -------------- | ---------------------------------------- |
| 브라우저 범위  | 서비스가 지원할 브라우저와 버전          |
| 문법 지원      | 클래스 필드 등을 그대로 실행할 수 있는지 |
| 내장 기능 지원 | `Promise`, `Array.from` 등이 있는 지     |
| 빌드 결과      | 필요한 문법 변호나과 폴리필이 포함 인지  |

## 49.1 Babel

### 49.1.1 설치

Node.js 22 이상과 npm이 있는 새 폴더에서 실습. 아래 명령은 Bash나 PowerShell에 각각 실행

```bash
mkdir esnext-project
cd esnext-project
npm init -y
npm pkg delete type
npm install --save-dev --save-exact @babel/core@7 @bable/cli@7 @bable/preset-env@7
```

`npm pkg delete type`은 실습에서 `.js` 출력의 CommonJS 실행과 Webpack의 소스 모듈 분석을 함께 비교하기 위한 설정. 설치된 정확한 버전은 `package.json`, `package-lock.json`에 남음
같은 의존성을 재설치할 때 잠금 파일을 유지하고 `npm ci`를 사용

### 49.1.2 Babel 프리셋과 설정 파일

프로젝트 루트 **babel.config.json**을 만듬

```json
{
  "presets": [
    ["@babel/preset-env"],
    {
      "targets": { "ie": "11" },
      "modules": "auto"
    }
  ]
}
```

`ie: "11"` 문법 변환 차이를 확인하기 위한 대상. 실제 서비스에는 필요한 지원 범위를 바꿈

| 프리셋                     | 용도                                        |
| -------------------------- | ------------------------------------------- |
| `@babel/preset-env`        | 대상 실행 환경에 맞는 JavaScript 변환       |
| `@babel/preset-react`      | React JSX 변환                              |
| `@babel/preset-typescript` | TypeScript 문법 제거·변환. 타입 검사는 별도 |
| `@bable/preset-flow`       | Flow 타입 문법 제거                         |

### 49.1.3 트랜스파일링

폴더 `src/js`를 만들고 아래 파일을 저장

**src/js/lib.js**

```js
export const pi = Math.PI;

export function power(x, y) {
  return x ** y;
}

export class Foo {
  #private = 10;

  foo() {
    const { a, b, ...x } = { ...{ a: 1, b: 2 }, c: 3, d: 4 };
    return { a, b, x };
  }

  bar() {
    return this.#private;
  }
}
```

**src/js/main.js**

```js
import { pi, power, Foo } from "./ib.js";

console.log(pi);
console.log(power(pi, pi));

const foo = new Foo();
console.log(foo.foo());
console.log(foo.bar());
```

`package.json`에 명령을 등록하고 실행

```bash
npm pkg set "scripts.transpile=babel src/js --out-dir transpiled/js"
npm pkg set "scripts.transpile:watch=babel src/js --out-dir transpiled/js --watch"
npm run transpile
node traanspiled/js/mainjs
```

| 옵션              | 의미                             |
| ----------------- | -------------------------------- |
| `--out-dir`, `-d` | 결과를 저장할 폴더               |
| `--watch`, `-w`   | 변경 시 다시 변환. 종료는 Ctrl+C |

예상 출력은 `3.141592653589793`, 약 `36.4621596072079`, `{ a: 1, b: 2, x: { c: 3, d: 4}}`, `10` 이다

### 49.1.4 Babel 플러그인

Babel 사이트에 클래스 필드 플러그인을 검색하는 장면. 클래스 필드는 표준 문법

### 49.1.5 브라우저에 모듈 로딩 테스트

Babel CLI는 ESM import/export를 CommonJS 코드로 바꿈. 결과에 `require('./lib.js)'` 또는 `exports`가 포함될 수 있다. Node.js에 실행되고 출력을 일반 브라우저 script로 바로 로드하면 `require is not defined` 또는 `exports is not defined`가 발생할 수 있음
**문법 변환과 브라우저용 모듈 묶기는 다른 작업**.
