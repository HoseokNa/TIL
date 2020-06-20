# 리액트 컴포넌트 스타일링하기

벨로퍼트와 함께하는 모던 리액트 참고. [링크](https://react.vlpt.us/styling/)

# 목차

- [Sass](#Sass)
- [CSS Module](#CSS-Module)
- [styled-components](#styled-components)

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

# CSS Module

## CSS 클래스가 중첩되는 것을 방지

레거시 프로젝트에 리액트를 도입하게 될 때, 또는 클래스 이름 짓는 규칙을 정하기 힘든 상황이거나 번거로울 때 사용하면 편함.

이 기능은 webpack 에서 사용하는 css-loader 에서 지원되는데, CRA 로 만든 프로젝트에는 이미 적용 되어 있음.

CSS Module 를 사용 할 때 확장자를 `.module.css` 한다.

```css
.Box {
  background: black;
  color: white;
  padding: 2rem;
}
```

리액트 컴포넌트 파일에서 해당 CSS 파일을 불러올 때 CSS 파일에 선언한 클래스 이름들이 모두 고유해짐.

```js
import React from "react";
import styles from "./Box.module.css";

function Box() {
  return <div className={styles.Box}>{styles.Box}</div>;
}

export default Box;
```

className 을 설정 할 때에는 styles.Box 이렇게 import로 불러온 styles 객체 안에 있는 값을 참조.

다음과 같은 상황에 사용하면 유용.

- 레거시 프로젝트에 리액트를 도입할 때
- CSS 클래스를 중복되지 않게 작성하기 위하여 CSS 클래스 네이밍 규칙을 만들기 귀찮을 때

CSS Module 을 작성 할 때에는 중복될 일이 없기 때문에 `.icon`, `.checkbox` 같은 짧고 흔한 이름을 사용해도 상관이 없음.

만약 클래스 이름에 - 가 들어가 있다면 다음과 같이 사용해야함. `styles['my-class']`

classnames 의 bind 기능을 사용하면, CSS 클래시 이름을 지정해 줄 때 cx('클래스이름') 과 같은 형식으로 편하게 사용 할 수 있음.

```js
import styles from "./CheckBox.module.css";
import classNames from "classnames/bind";

const cx = classNames.bind(styles);

cx("one", "two");
cx("my-component", {
  condition: true,
});
cx("my-component", ["another", "classnames"]);
```

CSS Module 은 Sass 에서도 사용 가능.(`node-sass` 설치)

CSS Module 을 사용하고 있는 파일에서 클래스 이름을 고유화 하지 않고 전역적 클래스이름을 사용하고 싶다면

```scss
// css
:global .my-global-name {
}

// scss
:global {
  .my-global-name {
  }
}
```

반대로, CSS Module 을 사용하지 않는 곳에서 특정 클래스에서만 고유 이름을 만들어서 사용하고 싶다면

```scss
// css
:local .make-this-local {
}

// scss
:local {
  .make-this-local {
  }
}
```

# styled-components

## CSS in JS

JS 안에 CSS 를 작성하는 것.

CSS in JS 관련 리액트 라이브러리. (그 외 `emotion`, `styled-jsx` 등 있음)

Tagged Template Literal 사용.

```js
// ex
const StyledDiv = styled`
  background: ${(props) => props.color};
`;
```

```js
 yarn add styled-components
```

```js
import React from "react";
import styled from "styled-components";

const Circle = styled.div`
  width: 5rem;
  height: 5rem;
  background: ${(props) => props.color || "black"};
  border-radius: 50%;
`;

function App() {
  return <Circle color="blue" />;
}

export default App;
```

styled-components 를 사용하면 이렇게 스타일을 입력함과 동시에 해당 스타일을 가진 컴포넌트를 만들 수 있음.

여러 줄의 CSS 코드를 조건부로 보여주고 싶다면 `css` 를 사용.

```js
import React from "react";
import styled, { css } from "styled-components";

const Circle = styled.div`
  width: 5rem;
  height: 5rem;
  background: ${(props) => props.color || "black"};
  border-radius: 50%;
  ${(props) =>
    props.huge &&
    css`
      width: 10rem;
      height: 10rem;
    `}
`;

function App() {
  return <Circle color="red" huge />;
}

export default App;
```

### ThemeProvider

styled-components 로 만드는 모든 컴포넌트에서 조회하여 사용 할 수 있는 전역적인 값을 설정.

```js
import React from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

function App() {
  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <AppBlock>
        <Button>BUTTON</Button>
      </AppBlock>
    </ThemeProvider>
  );
}

export default App;
```

이렇게 `theme` 을 설정하면 `ThemeProvider`내부에 렌더링된 styled-components 로 만든 컴포넌트에서 `palette` 를 조회하여 사용.

`ThemeProvider` 로 설정한 값은 styled-components 에서 `props.theme` 로 조회.

```js
import React from "react";
import styled, { css } from "styled-components";
import { darken, lighten } from "polished";

const StyledButton = styled.button`
  /* 공통 스타일 */
  display: inline-flex;
  outline: none;
  border: none;
  border-radius: 4px;
  color: white;
  font-weight: bold;
  cursor: pointer;
  padding-left: 1rem;
  padding-right: 1rem;

  /* 크기 */
  height: 2.25rem;
  font-size: 1rem;

  /* 색상 */
  ${(props) => {
    const selected = props.theme.palette[props.color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}

  /* 기타 */
  & + & {
    margin-left: 1rem;
  }
`;

function Button({ children, ...rest }) {
  return <StyledButton {...rest}>{children}</StyledButton>;
}

Button.defaultProps = {
  color: "blue",
};

export default Button;
```

리팩토링

```js
// ...

  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}

// ...
```

리팩토링

```js
const colorStyles = css`
  ${({ theme, color }) => {
    const selected = theme.palette[color];
    return css`
      background: ${selected};
      &:hover {
        background: ${lighten(0.1, selected)};
      }
      &:active {
        background: ${darken(0.1, selected)};
      }
    `;
  }}
`;

const StyledButton = styled.button`
  /* ... */

  /* 색상 */
  ${colorStyles}
`;

// ...
```

이렇게 분리해두면 나중에 유지보수를 할 때 더 편해짐.

### Dialog

```js
import React from "react";
import styled from "styled-components";
import Button from "./Button";

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

function Dialog({ title, children, confirmText, cancelText }) {
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <Button color="gray">{cancelText}</Button>
          <Button color="pink">{confirmText}</Button>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```

styled-components로 컴포넌트의 스타일을 특정 상황에서 덮어쓰기.

```js
// Dialog.js

// ...

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({ title, children, confirmText, cancelText }) {
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray">{cancelText}</ShortMarginButton>
          <ShortMarginButton color="pink">{confirmText}</ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

// ...
```

컴포넌트의 스타일을 커스터마이징 할 때에는 해당 컴포넌트에서 `className` props 를 내부 엘리먼트에게 전달이 되고 있는지 확인.

```js
const MyComponent = ({ className }) => {
  return <div className={className}></div>;
};

const ExtendedComponent = styled(MyComponent)`
  background: black;
`;
```

```js
// Dialog.js
import React from "react";
import styled from "styled-components";
import Button from "./Button";

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({
  title,
  children,
  confirmText,
  cancelText,
  onConfirm,
  onCancel,
  visible,
}) {
  if (!visible) return null;
  return (
    <DarkBackground>
      <DialogBlock>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray" onClick={onCancel}>
            {cancelText}
          </ShortMarginButton>
          <ShortMarginButton color="pink" onClick={onConfirm}>
            {confirmText}
          </ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```

```js
// App.js
import React, { useState } from "react";
import styled, { ThemeProvider } from "styled-components";
import Button from "./components/Button";
import Dialog from "./components/Dialog";

const AppBlock = styled.div`
  width: 512px;
  margin: 0 auto;
  margin-top: 4rem;
  border: 1px solid black;
  padding: 1rem;
`;

const ButtonGroup = styled.div`
  & + & {
    margin-top: 1rem;
  }
`;

function App() {
  const [dialog, setDialog] = useState(false);
  const onClick = () => {
    setDialog(true);
  };
  const onConfirm = () => {
    console.log("확인");
    setDialog(false);
  };
  const onCancel = () => {
    console.log("취소");
    setDialog(false);
  };

  return (
    <ThemeProvider
      theme={{
        palette: {
          blue: "#228be6",
          gray: "#495057",
          pink: "#f06595",
        },
      }}
    >
      <>
        <AppBlock>
          <Button size="large" color="pink" fullWidth onClick={onClick}>
            삭제
          </Button>
        </AppBlock>
        <Dialog
          title="정말로 삭제하시겠습니까?"
          confirmText="삭제"
          cancelText="취소"
          onConfirm={onConfirm}
          onCancel={onCancel}
          visible={dialog}
        >
          데이터를 정말로 삭제하시겠습니까?
        </Dialog>
      </>
    </ThemeProvider>
  );
}

export default App;
```

### 트랜지션 구하기

트랜지션 효과를 적용 할 때에는 CSS `Keyframe` 을 사용하며, styled-components 에서 이를 사용 할 때에는 `keyframes` 라는 유틸을 사용.

```js
import React, { useState, useEffect } from "react";
import styled, { keyframes, css } from "styled-components";
import Button from "./Button";

const fadeIn = keyframes`
  from {
    opacity: 0
  }
  to {
    opacity: 1
  }
`;

const fadeOut = keyframes`
  from {
    opacity: 1
  }
  to {
    opacity: 0
  }
`;

const slideUp = keyframes`
  from {
    transform: translateY(200px);
  }
  to {
    transform: translateY(0px);
  }
`;

const slideDown = keyframes`
  from {
    transform: translateY(0px);
  }
  to {
    transform: translateY(200px);
  }
`;

const DarkBackground = styled.div`
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(0, 0, 0, 0.8);

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${fadeIn};
  animation-fill-mode: forwards;

  ${(props) =>
    props.disappear &&
    css`
      animation-name: ${fadeOut};
    `}
`;

const DialogBlock = styled.div`
  width: 320px;
  padding: 1.5rem;
  background: white;
  border-radius: 2px;
  h3 {
    margin: 0;
    font-size: 1.5rem;
  }
  p {
    font-size: 1.125rem;
  }

  animation-duration: 0.25s;
  animation-timing-function: ease-out;
  animation-name: ${slideUp};
  animation-fill-mode: forwards;

  ${(props) =>
    props.disappear &&
    css`
      animation-name: ${slideDown};
    `}
`;

const ButtonGroup = styled.div`
  margin-top: 3rem;
  display: flex;
  justify-content: flex-end;
`;

const ShortMarginButton = styled(Button)`
  & + & {
    margin-left: 0.5rem;
  }
`;

function Dialog({
  title,
  children,
  confirmText,
  cancelText,
  onConfirm,
  onCancel,
  visible,
}) {
  const [animate, setAnimate] = useState(false);
  const [localVisible, setLocalVisible] = useState(visible);

  useEffect(() => {
    // visible 값이 true -> false 가 되는 것을 감지
    if (localVisible && !visible) {
      setAnimate(true);
      setTimeout(() => setAnimate(false), 250);
    }
    setLocalVisible(visible);
  }, [localVisible, visible]);

  if (!animate && !localVisible) return null;
  return (
    <DarkBackground disappear={!visible}>
      <DialogBlock disappear={!visible}>
        <h3>{title}</h3>
        <p>{children}</p>
        <ButtonGroup>
          <ShortMarginButton color="gray" onClick={onCancel}>
            {cancelText}
          </ShortMarginButton>
          <ShortMarginButton color="pink" onClick={onConfirm}>
            {confirmText}
          </ShortMarginButton>
        </ButtonGroup>
      </DialogBlock>
    </DarkBackground>
  );
}

Dialog.defaultProps = {
  confirmText: "확인",
  cancelText: "취소",
};

export default Dialog;
```
