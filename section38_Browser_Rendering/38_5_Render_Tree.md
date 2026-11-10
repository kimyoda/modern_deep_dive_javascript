## 38.5 렌더 트리 생성

### DOM과 스타일 결합

DOM과 스타일 정보를 이용해 화면 배치에 필요한 구조를 만듬

### DOM, 스타일 결합

```mermaid
flowchart TD
  D["DOM"] --> S["스타일 계산·박스 생성"]
  C["CSSOM·캐스케이드·상속"] --> S
  S --> B["body 박스"]
  B --> U["ul 박스"]
  U --> A["Apple 항목 박스"]
  U --> N["Banana 박스"]
  U --> O["Orange 박스"]
```

| 예시                              | DOM | 공간     |
| --------------------------------- | --- | -------- |
| 일반적인 li                       | O   | O        |
| head 안 meta·script               | O   | 보통없음 |
| display: none 요소와 그 하위 영역 | O   | 없음     |
| visibility: hidden 요소           | O   | 유지     |
| opacity: 0 요소                   | O   | 유지     |

DOM 요소와 렌더링 박스도 언제나 일대일 관계는 아님

### Layout·Paint·Composite

```mermaid
flowchart TD
  D["DOM"] --> S["스타일 계산·렌더링 구조"]
  C["CSSOM"] --> S
  S --> L["Layout: 위치·크기 계산"]
  L --> P["Paint: 그릴 내용 생성·래스터화 등"]
  P --> M["Composite:레이어 합성"]
  M --> V["회면 표시"]
```

브라우저 구현에서 페인트 명령 생성·래스터화 등이 더 세분화될 수 있음. 그림은 학습용 핵심 흐름. 최초 표시 이후에 변경에 필요한 단계가 반복
