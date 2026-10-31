## 29.1 Math 프로퍼티

### 29.1.1 Math.PI

원의 둘레와 지름의 비인 원주율의 근사값

```JS
console.log(Math.PI); // 3.141592653589793

const radius = 3;
const circumference = 2 * Math.PI * radius;
const area = Math.PI * radius ** 2;

console.log(circumferece.toFixed(2)); // '18.35'
console.log(area.toFixed(2)); // '28.27'
```

| 계산      | 식             |
| --------- | -------------- |
| 원의 둘레 | 2 × π × 반지름 |
| 원의 넓이 | π × 반지름²    |

`Math.PI`는 무한한 자릿수 원주율 전체가 아닌 Number로 표현한 근삿값. `toFixed` 결과는 28장에서 배운 **문자열**
