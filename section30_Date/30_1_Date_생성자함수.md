## 30.1 Date 생성자 함수

### 30.1.1 new Date()

현재 시각을 나타내는 Date 객체를 만듬. Date를 new 없이 호출하면 현재 날짜·시간의 문자열을 반환

```js
const now = new Date();

console.log(now instanceof Date); // true
console.log(Number.isFinite(now.getTime())); // true
console.log(typeof Date()); // string
```

### 30.1.2 new Date(milliseconds)

기준 시각부터 경과한 밀리초를 전달. 1초는 1000밀리초

```js
console.log(new Date(0).toISOstring()); // '1970-01-01T00:00:00.000Z'
console.log(new Date(864000000).toISOstring()); // '1970-01-02T00:00:00.000Z'
console.log(new Date(2020).getTime()); // '2020'
```

**숫자 인수 하나는 연도가 아니라 밀리초** new Date(2020)은 2020년이 아님. API가 초 단위 타임스탬프를 제공하면 밀리치로 변환

### 30.1.3 new Date(dateString)

문자열을 해석하여 날짜를 만듬. API 등에서 ISO 형식과 시간대 오프셋을 명시

```js
const utc = new Date("2020-07-24T03:30:00Z");
const kst = new Date("2020-07-24T12:30:00+09:00");

console.log(utc.getTime() === kst.getTime()); // true
console.log(new Date("2020-07-24").toISOString()); // '2020-07-24T00:00:00.000Z'
console.log(new Date("2020-07-24t12:30:00").getHours()); // 12: 로컬 시각 해석
```

| 문자열                      | 해석                            |
| --------------------------- | ------------------------------- |
| 2020-07-24T03:30:00Z        | UTC                             |
| 2020-07-24T12:30:00+09:00   | UTC+09:00 오프셋                |
| 2020-07-24                  | 표준 날짜 전용 형식은 UTC 자정  |
| 2020-07-24T12:30:00         | 오프셋 없는 날짜·시간은 로컬    |
| 2020/07/24 등의 비표준 형식 | 환경별 해석 차이가 있을 수 있다 |

### 30.1.4 new Date(year, month, day, hour, minute, second, millisecond)

두 개 이상의 숫자 인수는 **로컬 날짜·시간** 으로 해석. 월은 0부터 시작.

| 인수                              | 기본값·범위                              |
| --------------------------------- | ---------------------------------------- |
| year                              | 연도, 0~99또는 1900~1999로 해석하는 특례 |
| month                             | 0~11, 0은 1월                            |
| day                               | 생략하면 1                               |
| hour, minute, second, millisecond | 생략하면 0                               |

```js
const march = new Date(2020, 2);
const date = new Date(2020, 6, 24, 12, 30, 10, 150);

console.log(march.getMonth()); // 2: 3월
console.log(march.getDate()); // 1
console.log(date.getFullYear()); // 2020
console.log(date.getMonth()); // 6: 7월
console.log(date.getMilliseconds()); // 150
```

인수가 벗어나면 다음달·이전 달 등으로 보전될 수 있음. Date 생성은 엄격한 달력 날짜 검증과 다름

```js
console.log(new Date(2020, 12, 1).getFullYear()); // 2021
console.log(new Date(2020, 2, 0).getDate()); // 29: 2월 마지막날
```
