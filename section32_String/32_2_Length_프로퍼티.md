## 32.2 length 프로퍼티

UTF-16 코드 단위 수를 반환

```js
console.log("Hello".length); // 5
console.log("안녕하세요!".length); // 6
console.log("�".length); // 2
console.log([..."�"].length); // 1
console.log("e\u0301".length); // 2
```

| 기준        | 예시                             | 의미                        |
| ----------- | -------------------------------- | --------------------------- |
| 코드 단위   | '�'.length는 2                   | length·일반 인덱스 기준     |
| 코드 포인트 | [...'�'].length는 1              | Unicode 숫자값 단위         |
| 그래핌      | e와 결합 악센트가 한 글자로 보임 | 사용자에게 보이는 글자 단위 |

Spread도 결합 문자나 복합 이모지를 모두 하나의 글자로 세지는 않음

```js
const text = "e\u0301";
const segmenter = new Intl.Segmenter("ko", { granularity: "grapheme" });

console.log([...text].length); // 2: 코드 포인트
console.log([...segmenter.segment(text)].length); // 1: 그래핌
```

Intl.Segmenter는 지원 환경에 사용. 글자 수 제한을 구현할 때 어떤 단위를 사용할지 서비스 정책을 정함.
