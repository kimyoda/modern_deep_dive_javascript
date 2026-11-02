## 30.2 Date 메서드

### 30.2.1 Date.now

현재 시각의 밀리초 타임스탬프를 숫자로 반환.

```js
console.log(typeof Date.now()); // number
console.log(Number.isFinite(Date.now())); // true
```

### 30.2.2 Date.parse

날짜 문자열을 밀리초 숫자로 변환. 해석에 실패하면 NaN

```js
console.log(Date.parse("1970-01-02T00:00:00Z")); // 86400000
console.log(Date.parse("1970-01-02T09:00:00+09:00")); // 86400000
console.log(Number.isNaN(Date.parse("not-a-date"))); // true
```

### 30.2.3 Date.UTC

구성 요소를 UTC 날짜·시간으로 해석 밀리초 숫자를 반환. Date 객체를 반환하는 것은 아님

```js
console.log(Date.UTC(1970, 0, 2)); // 86400000
console.log(new Date(Date.UTC(2020, 6, 24, 12)).getUTCHours()); // 12
console.log(typeof Date.UTC(2020, 6, 24)); // number
```

| 표현              | 반환값               | 기준                    |
| ----------------- | -------------------- | ----------------------- |
| new Date(y, m, d) | Date 객체            | 로컬                    |
| Date.UTC(y, m, d) | 밀리초 숫자          | UTC                     |
| Date.parse(text)  | 밀리초 숫자 또는 NaN | 문자열 형식에 따라 결정 |

### 30.2.4~30.2.18 날짜 구성 요소 읽기·설정

getDate는 월 날짜, getDay는 요일

| getter            | setter                                | 인수         |
| ----------------- | ------------------------------------- | ------------ |
| getFullYear()     | setFullYear(year, month?, day?)       | 연도         |
| getMonth()        | setMonth(month, day?)                 | 월 0~11      |
| getDate()         | setDate(day)                          | 월 날짜 1~31 |
| getDay()          | 없음                                  | 요일 0~6     |
| getHours()        | setHours(hour, minute?, second?, ms?) | 시 0~23      |
| getMinutes()      | setMinutes(minute, second?, ms?)      | 분 0~59      |
| getSeconds()      | setSetconds(second, ms?)              | 초 0~59      |
| getMilliseconds() | setMilliseconds(ms)                   | 밀리초 0~999 |

```js
const date = new Date("2020-07-24T12:30:10.150+09:00");

console.log([
  date.getFullYear(),
  date.getMonth(),
  date.getDate(),
  date.getDay(),
  date.getHours(),
  date.getMinutes(),
  date.getSeconds(),
  date.getMilliseconds(),
]);
// [2020, 6, 24, 5, 12, 30, 10, 150]: Asia/Seoul 환경
```

| getDay 결과 | 요일   |
| ----------- | ------ |
| 0           | 일요일 |
| 1           | 월요일 |
| 2           | 화요일 |
| 3           | 수요일 |
| 4           | 목요일 |
| 5           | 금요일 |
| 6           | 토요일 |

**setter는 원본 Date를 변경하고 변경 후 타임스탬프 숫자를 반환**

```js
const date = new Date(2020, 6, 24, 12, 30, 10, 150);

date.setFullYear(2021, 0, 1);
date.setMonth(1, 15);
date.setDate(20);
date.setHours(8, 0, 0, 0);
date.setMinutes(5, 10, 999);
date.setSeconds(30, 0);
const result = date.setMilliseconds(123);

console.log([
  date.getFullYear(),
  date.getMonth(),
  date.getDate(),
  date.getDay(),
  date.getHours(),
  date.getMinutes(),
  date.getSeconds(),
  date.getMilliseconds(),
]); // [2021, 20, 8, 5, 30, 123]
console.log(typeof result); // number
```

UTC 구성 요소는 getUTCFullYear·getUTCMonth·getUTCDate·getUTCDay·getUTCHours·getUTCMinutes·getUTCSeconds·getUTCMilliseconds를 사용. 대응하는 setUTC 계열도 있고 요일 setter는 없음

### 30.2.19 getTime/ 30.2.20 setTime

```js
const date = new Date(0);
const timestamp = date.setTime(86400000);

console.log(date.getTime()); // 86400000
console.log(timestamp); // 86400000
console.log(date.toISOString()); // '1970-01-02T00:00:00.00Z'
```

### 30.2.21 getTimezoneOffset

**UTC - 실행 환경 로컬 시간**차이를 분 단위로 반환. 해당 날짜의 시간대 규칙을 사용

```js
const date = new Date("2020-07-24T00:00:00Z");
console.log(date.getTimezoneOffset()); // -540: Asia/Seoul 환경
```

KST는 UTC보다 9시간 빨라 -540이다. 입력 문자열에 적은 오프셋을 돌려주는 메서드가 아님. 다른 지역에서는 값이 달라질 수 있음

### 30.2.22~30.2.26 문자열 표시

| 메서드                              | 결과 기준              |
| ----------------------------------- | ---------------------- |
| toDateString()                      | 로컬 날짜 문자열       |
| toTimeString()                      | 로컬 시간 문자열       |
| toISOString()                       | UTC의 ISO 문자열, 끝 Z |
| toLocaleString(locale, options)     | 로컬 형식의 날짜·시간  |
| toLocaleTimeString(locale, options) | 로컬 형식의 시간       |

```js
const date = new Date("2020-07-24T12:30:00+09:00");

console.log(date.toDateString()); // 'Fri Jul 24 2020': Asia/Seoul 환경
console.log(date.toTimeString().startsWith("12:30:00")); // true: Asia/Seoul 환경
console.log(date.toISOString()); // '2020-07-24T03:30:00.000Z'
console.log(typeof date.toLocaleString("ko-KR", { timeZone: "Asia/Seoul" })); // string
console.log(typeof date.toLocaleTimeString("en-US", { timeZone: "UTC" })); // string
```

**로캘과 시간대는 다름** ko-KR은 한국어 표시 형식 timeZone을 지정하지 않으면 실행 환경의 시간대를 사용.

## 30.3 Date를 활용한 시계 예제

12시간제는 자정·정오를 12로 표시. 타이머를 멈추는 함수도 반환한다

```js
function formatClock(date) {
  const days = ["일", "월", "화", "수", "목", "금", "토"];
  const pad = (value) => String(value).padStart(2, "0");
  const hour = date.getHours();
  const period = hour >= 12 ? "오후" : "오전";
  const displayHour = hour % 12 || 12;

  return (
    `${date.getFullYear()}-${pad(date.getMonth() + 1)}-${pad(date.getDate())}` +
    ` (${days[date.getDay()]}) ${period} ${pad(displayHour)}` +
    `:${pad(date.getMinutes())}:${pad(date.getSeconds())}`
  );
}

function startClock(onTick = console.log) {
  const tick = () => onTick(formatClock(new Date()));
  tick();
  const timer = setInterval(tick, 1000);
  return () => clearInterval(timer);
}

console.log(formatClock(new Date(2020, 6, 24, 12, 30, 10)));
// '2020-07-24 (금) 오후 12:30:10': Asia/Seoul 환경

// 실행: const stop = startClock()
// 종료: stop();
```

타이머는 정확한 시계 장치가 아니라 이전 값에 1초를 더하지 않고 매번 현재 시각을 다시 읽음

## 질문, 답변

### 1. Date는 내부적으로 무엇을 저장하는지

- UTC 기준 시각부터 경과한 밀리초 타임스탬프. 잘못된 Date 시간값은 NaN이다

### 2. Date 객체에 Asia/Seoul 같은 시간대가 저장되는 지

- 아니다. 시각을 저장하며 로컬 메서드는 실행 환경의 시간대를 사용

### 3. new Date(2020)은 2020년인지

- 아니다. 기준 시각에서 2020밀리초 뒤

### 4. 구성 요소 생성자의 월은 몇부터 시작하는지

- 0부터. 0은 1월, 11은 12월

### 5. getDate, getDay ckdl

- 월의 날짜와 요일. 요일은 일요일 0부터 토요일 6.

### 5. setter는 새 Date를 반환하는지

- 아니다. 원본을 변경하고 변경 후 타임스탬프 숫자를 반환.
