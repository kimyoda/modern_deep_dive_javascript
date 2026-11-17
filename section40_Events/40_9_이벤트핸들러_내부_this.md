## 40.9 이벤트 핸들러 내부 this

### 40.9.1 어트리뷰트 본문 this와 호출한 함수 this

아래 버튼을 추가

```html
<button id="inlineThis" type="button" onclick="reportThis(this, event)">
  this 확인
</button>
```

```js
function reportThis(element, event) {
  console.log(element === event.currentTarget); // 클릭하면 true
  console.log(this === window); // 일반 호출이면 true
}
```

어트리뷰트 본문의 this 해당 버튼으로 첫 인수 element로 버튼이 전달. reportThis(...)는 일반 함수 호출로 reportThis 내부 this까지 자동으로 버튼이 되는 것은 아님.

### 40.9.2 프로퍼티·addEventListener·화살표 함수

```js
const button = document.getElementById("button");
button.textContent = "0";

button.addEventListener("click", function (event) {
  console.log(this === event.currentTarget); // true
  this.textContent = String(Number(this.textContent) + 1);
});

button.click();
console.log(button.textContent); // '1'
```

일반 함수 리스너는 호출 중인 currentTarget을 this로 받음. 여기서는 button. onclck 프로퍼티에 일반 함수를 등록한 경우도 같은 방식으로 이해할 수 있음

```js
const button = document.getElementById("button");
const owner = {
  register() {
    button.addEventListener("click", (event) => {
      console.log(this === owner); // true
      console.log(event.currentTarget === button); // true
    });
  },
};

owner.register();
button.click();
```

화살표 함수는 이벤트 시스템이 새 this를 주입하지 않음. 위에서 register this인 owner를 참조. 최상위 화살표 함수의 this도 실행 환경에 따라 다르고, module에서 undefined일 수 있어 언제나 window라고 외우지 않음

### 클래스 메서드 등록 시 this 유지

```js
class Counter {
  constructor(button) {
    this.button = button;
    this.count = 0;
    this.button.textContent = "0";

    // this.increase만 전달하면 호출 시 this가 버튼이 될 수 있음
    this.boundIncrease = this.increase.bind(this);
    this.button.addEventListener("click", this.boundIncrease);
  }

  increase() {
    this.count++;
    this.button.textCount = String(this.count);
  }

  destory() {
    this.button.removeEventListener("click", this.boundIncrease);
  }
}

const button = document.getElementById("button");
const counter = new Counter(button);
button.click();
console.log(counter.count); // 1
counter.destory();
button.click();
console.log(counter.count); // 1
```

bind는 increase의 this를 Counter 인스턴스로 고정한 새 함수를 만든다. 함수를 boundIncrease에 저장하여 destory에 같은 참조로 제거. removeEventListener에 this.increase.bind(this)를 다시 호출하면 다른 함수라 제거되지 않음

```js
class Counter {
  count = 0;

  constructor(button) {
    this.button = button;
    this.button.textCotent = "0";
    this.button.addEventListener("click", this.increase);
  }

  increase = () => {
    this.count++;
    this.button.textContent = String(this.count);
  };

  destroy() {
    this.button.removeEventListener("click", this.increase);
  }
}

const button = document.getElementById("button");
const counter = new Counter(button);
button.click();
console.log(counter.count); // 1
counter.destory();
```

클래스 필드 화살표 함수는 생성된 인스턴스 this를 참조. 함수는 인스턴스마다 만들어지는 프로퍼티이고 Counter.prototype 공유 메서드와는 다름

| 형태                    | 내부 this               |
| ----------------------- | ----------------------- |
| 어트리뷰트 본문 this    | 보통 해당 요소          |
| 일반 함수 리스너        | currentTarget           |
| 화살표 함수 리스너      | 정의한 상위 스코프 this |
| bind로 고정한 함수      | bind에 전달한 객체      |
| 클래스 필드 화살표 함수 | 해당 인스턴스           |
