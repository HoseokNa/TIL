# 리액트 컴포넌트 스타일링하기

벨로퍼트와 함께하는 모던 리액트 참고. [링크](https://react.vlpt.us/styling/)

# 목차

- [Sass](#Sass)

# Sass

## 스타일 파일에 다양한 유용한 문법을 사용해서 컴포넌트 스타일링 생산성을 높이기

Sass (Syntactically Awesome Style Sheets: 문법적으로 짱 멋진 스타일시트) 는 CSS pre-processor.

복잡한 작업을 쉽게 할 수 있고, 코드의 재활용성을 높여줌. 코드의 가독성을 높아 유지보수가 쉬워짐.

Sass 에서는 두가지의 확장자 (.scss/.sass) 를 지원.

```scss
// sass
$font-stack:    Helvetica, sans-serif
$primary-color: #333

body
  font: 100% $font-stack
  color: $primary-color

// scss
$font-stack:    Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

보통 scss 문법이 더 많이 사용. (기존 css랑 구조가 비슷)

### node-sass 설치

Sass를 css로 변환 해줌

```js
yarn add node-sass
```

### Button.scss

```scss
$blue: #228be6; // 주석 선언

.Button {
  display: inline-flex;
  color: white;
  font-weight: bold;
  outline: none;
  border-radius: 4px;
  border: none;
  cursor: pointer;

  height: 2.25rem;
  padding-left: 1rem;
  padding-right: 1rem;
  font-size: 1rem;

  background: $blue; // 주석 사용
  &:hover {
    background: lighten($blue, 10%); // 색상 10% 밝게
  }

  &:active {
    background: darken($blue, 10%); // 색상 10% 어둡게
  }
}
```

// 주석 사용 가능

`$blue: #228be6;` 처럼 변수 사용 가능

lighten() 또는 darken() 과 같이 색상을 더 밝게하거나 어둡게 해주는 함수도 사용 가능.

`&`는 자신을 의미(.Button)

### 사이즈 조정하기

```js
import React from "react";
import "./Button.scss";

function Button({ children, size }) {
  return <button className={["Button", size].join(" ")}>{children}</button>;
}

Button.defaultProps = {
  size: "medium",
};

export default Button;
```

className 에 CSS 클래스 이름을 동적으로 넣어주기

```js
className={['Button', size].join(' ')}

// or
className={`Button ${size}`}
```

`classnames` 라이브러리 사용하면 편리

```js
classNames("foo", "bar"); // => 'foo bar'
classNames("foo", { bar: true }); // => 'foo bar'
classNames({ "foo-bar": true }); // => 'foo-bar'
classNames({ "foo-bar": false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'
classNames(["foo", "bar"]); // => 'foo bar'

// 동시에 여러개의 타입으로 받아올 수 도 있습니다.
classNames("foo", { bar: true, duck: false }, "baz", { quux: true }); // => 'foo bar baz quux'

// false, null, 0, undefined 는 무시됩니다.
classNames(null, false, "bar", undefined, 0, 1, { baz: null }, ""); // => 'bar 1'
```

```js
import React from "react";
import classNames from "classnames";
import "./Button.scss";

function Button({ children, size }) {
  return <button className={classNames("Button", size)}>{children}</button>;
}

Button.defaultProps = {
  size: "medium",
};

export default Button;
```

```js
import React from "react";
import "./App.scss";
import Button from "./components/Button";

function App() {
  return (
    <div className="App">
      <div className="buttons">
        <Button size="large">BUTTON</Button>
        <Button>BUTTON</Button>
        <Button size="small">BUTTON</Button>
      </div>
    </div>
  );
}

export default App;
```

Button.scss

```scss
$blue: #228be6;

.Button {
  display: inline-flex;
  color: white;
  font-weight: bold;
  outline: none;
  border-radius: 4px;
  border: none;
  cursor: pointer;

  // 사이즈 관리
  &.large {
    height: 3rem;
    padding-left: 1rem;
    padding-right: 1rem;
    font-size: 1.25rem;
  }

  &.medium {
    height: 2.25rem;
    padding-left: 1rem;
    padding-right: 1rem;
    font-size: 1rem;
  }

  &.small {
    height: 1.75rem;
    font-size: 0.875rem;
    padding-left: 1rem;
    padding-right: 1rem;
  }

  background: $blue;
  &:hover {
    background: lighten($blue, 10%);
  }

  &:active {
    background: darken($blue, 10%);
  }

  & + & {
    margin-left: 1rem;
  }
}
```

마지막 `& + &`은(`.Button + .Button`) 만약 버튼이 함께 있다면 우측에 있는 버튼에 스타일 적용 의미.

### mixin

반복이 되는 코드를 재사용하기.

```scss
&.blue {
  background: $blue;
  &:hover {
    background: lighten($blue, 10%);
  }

  &:active {
    background: darken($blue, 10%);
  }
}

&.gray {
  background: $gray;
  &:hover {
    background: lighten($gray, 10%);
  }

  &:active {
    background: darken($gray, 10%);
  }
}

&.pink {
  background: $pink;
  &:hover {
    background: lighten($pink, 10%);
  }

  &:active {
    background: darken($pink, 10%);
  }
}
```

```scss
$blue: #228be6;
$gray: #495057;
$pink: #f06595;

@mixin button-color($color) {
  background: $color;
  &:hover {
    background: lighten($color, 10%);
  }
  &:active {
    background: darken($color, 10%);
  }
}

//...

// 색상 관리
&.blue {
  @include button-color($blue);
}

&.gray {
  @include button-color($gray);
}

&.pink {
  @include button-color($pink);
}

//...
```

### outline 옵션 만들기

outline 이라는 옵션을 주면 버튼에서 테두리만 보여지도록 설정을 해보기.

```js
<button className={classNames("Button", size, color, { outline })}>
  {children}
</button>
```

이렇게 하면 outline 값이 true 일 때에만 button 에 outline CSS 클래스가 적용.

```scss
@mixin button-color($color) {
  background: $color;
  &:hover {
    background: lighten($color, 10%);
  }
  &:active {
    background: darken($color, 10%);
  }
  &.outline {
    color: $color;
    background: none;
    border: 1px solid $color;
    &:hover {
      background: $color;
      color: white;
    }
  }
}
```

기존에 button-color mixin 을 만들었었기 때문에 작업이 굉장히 간단.

### ..rest props

만약에 완성한 버튼 컴포넌트에 onClick 을 설정해주고 싶다면 어떻게 해야 할까?

```js
function Button({ children, size, color, outline, fullWidth, onClick }) {
  return (
    <button
      className={classNames('Button', size, color, { outline, fullWidth })}
      onClick={onClick}
    >
      {children}
    </button>
}
```

만약 onMouseMove 이벤트가 추가되면?

```js
function Button({
  children,
  size,
  color,
  outline,
  fullWidth,
  onClick,
  onMouseMove,
}) {
  return (
    <button
      className={classNames("Button", size, color, { outline, fullWidth })}
      onClick={onClick}
      onMouseMove={onMouseMove}
    >
      {children}
    </button>
  );
}
```

점점 복잡해짐.

spread 와 rest 사용해서 수정하기.

```js
function Button({ children, size, color, outline, fullWidth, ...rest }) {
  return (
    <button
      className={classNames("Button", size, color, { outline, fullWidth })}
      {...rest}
    >
      {children}
    </button>
  );
}
```

컴포넌트가 어떤 props 를 받을 지 확실치는 않지만 그대로 다른 컴포넌트 또는 HTML 태그에 전달을 해주어야 하는 상황에 사용하자.

### className이 겹치지 않게 작성하는 팁

1. 컴포넌트의 이름을 고유하게 지정.
2. 최상위 엘리먼트의 클래스 이름을 컴포넌트 이름과 똑같게(대문자로 시작)
3. 그 내부에서 셀렉터 사용.

```scss
.UserProfile {
  .user {
    img {
    }
    .username {
    }
  }
  .about {
  }
}
```
