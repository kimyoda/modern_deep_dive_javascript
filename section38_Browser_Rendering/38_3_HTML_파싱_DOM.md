## 38.3 HTML 파싱과 DOM 생성

index.html·style.css·app.js는 같은 폴더에 작성하는 기본 예제

### index.html

```html
<!doctype html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <title>브라우저 렌더링 실습</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <ul id="fruits">
      <li id="apple">Apple</li>
      <li id="banana">Banna</li>
      <li id="orange">Orange</li>
    </ul>
    <script src="app.js"></script>
  </body>
</html>
```

### HTML에서 DOM으로

| 단계        | 내용                                    |
| ----------- | --------------------------------------- |
| 바이트 수신 | 네트워크 등에서 HTML 바이트를 받음      |
| 디코딩      | 결정한 문자 인코딩으로 문자 해석        |
| 토큰화      | 시작 태그·종료 태그·문자 등의 토큰 생성 |
| 트리 구성   | 토큰을 처리하여 노드를 생성·연결        |
| DOM         | 문서 구조를 나타내는 노드 트리 형성     |

인코딩은 HTTP 헤더·BOM·HTML의 meta 선언 등 관련 규칙으로 결정. meta charset을 적었다고 서버의 HTTP 헤더가 자동으로 설정된 것은 아님

다음은 공백 텍스트·DOCTYPE·title 등을 생략한 핵심 DOM 트리

```mermaid
flowchart TD
  D["document"] --> H["html"]
  H --> HEAD["head"]
  H --> BODY["head"]
  HEAD --> META["meta: charset=UTF-8"]
  HEAD --> LINK["link: stylesheet=style.css"]
  BODY --> UL["ul: id=fruits"]
  BODY --> SCRIPT["script: src=app.js"]
  UL --> A["li: id=apple"]
  UL --> B["li: id=banana"]
  UL --> C["li: id=orange"]
  A --> AT["텍스트: Apple"]
  B --> BT["텍스트: Banana"]
  C --> CT["텍스트: Orange]
```

id·src 같은 어트리뷰트는 요소의 어트리뷰트. HTML은 전체 응답이 도착한 뒤에만 파싱하는 것이 아닌 수신 중인 내용을 점진적으로 처리할 수 있음. 태그 하나가 DOM 노드 하나와 언제나 일대일로 대응하는것도 아님
