# React 기초

React 공부. 기본 내용 정리.

벨로퍼트와 함께하는 모던 리액트 참고. [링크](https://react.vlpt.us/)

# 목차

- [왜 React가 개발 됐을까](#왜-React가-개발-됐을까)
- [JSX](#JSX)

# 왜 React가 개발 됐을까

## 리액트는 어떠한 상태가 바뀌었을때 아예 다 날려버리고 처음부터 모든걸 새로 만들어서 보여준다면 어떨까? 라는 아이디어에서 개발이 시작됨.

대부분의 경우 웹 애플리케이션의 규모가 커지면, DOM 을 직접 건드리면서 작업을 하기에 코드가 난잡해짐.

그래서, Ember, Backbone, AngularJS 등의 프레임워크가 만들어짐. 이 프레임워크들은 작동방식이 각각 다르지만, 쉽게 설명하자면 자바스크립트의 특정 값이 바뀌면 특정 DOM의 속성이 바뀌도록 연결을 해주어서, 업데이트 하는 작업을 간소화해주는 방식으로 웹개발의 어려움을 해결함.

반면에 **리액트**는 어떠한 상태가 바뀌었을때, 그 상태에 따라 DOM 을 어떻게 업데이트 할 지 규칙을 정하는 것이 아니라, **아예 다 날려버리고 처음부터 모든걸 새로 만들어서 보여준다면 어떨까?** 라는 아이디어에서 개발이 시작됨.

그러면 "업데이트를 어떻게 해야 할 지" 에 대한 고민을 전혀 안해도 되기 때문에 개발이 정말 쉬워짐.

하지만, 모든걸 다 날려버리고 모든걸 새로 만들게 된다면, 속도가 굉장히 느릴 것.

하지만, 리액트에서는 **Virtual DOM** 이라는 것을 사용.

Virtual DOM 은 가상의 DOM. 그냥 JavaScript 객체이기 때문에 작동 성능이 실제로 브라우저에서 DOM 을 보여주는 것 보다 속도가 훨씬 빠름. 리액트는 상태가 업데이트 되면, 업데이트가 필요한 곳의 UI 를 Virtual DOM 을 통해서 렌더링함. 그리고 나서 리액트 개발팀이 만든 매우 효율적인 비교 알고리즘을 통해 차이가 있는 곳을 감지하여 이를 실제 DOM 에 패치.

이를 통하여, "업데이트를 어떻게 할 지" 에 대한 고민을 하지 않으면서, 빠른 성능도 지켜냄.

# JSX

## JSX 는 리액트에서 생김새를 정의할 때, 사용하는 문법

얼핏보면 HTML 같이 생겼지만 실제로는 JavaScript.

```js
return <div>안녕하세요</div>;
```

리액트 컴포넌트 파일에서 XML 형태로 코드를 작성하면 babel 이 JSX 를 JavaScript 로 변환함.

### JSX 가 JavaScript 로 제대로 변환이 되려면 지켜주어야 하는 몇가지 규칙 있음

- **태그는 꼭 닫혀있어야 함.**

  - intput이나 br도 닫아줘야함. `<input>` X `<input />` O

- **두 개 이상의 태그는 하나의 태그로 감싸져야 함.**
  - 불필요한 div 태그 사용 대신 Fragment 사용 가능.
  - Fragment는 이름 없이 작성한 태그. 브라우저상에서 따로 별도의 엘리먼트로 나타나지 않음

```js
return (
  <>
    <Hello />
    <div>안녕히계세요</div>
  </>
);
```

### JSX 내부에 자바스크립트 변수를 보여줘야 할 때에는 {} 으로 감싸서 보여줌.

```js
import React from "react";
import Hello from "./Hello";

function App() {
  const name = "react";
  return (
    <>
      <Hello />
      <div>{name}</div>
    </>
  );
}

export default App;
```

### style 과 className

인라인 스타일은 객체 형태로 작성을 해야 함. background-color 처럼 - 로 구분되어 있는 이름들은 backgroundColor 처럼 camelCase 형태로 네이밍 해애함.

```js
import React from "react";
import Hello from "./Hello";

function App() {
  const name = "react";
  const style = {
    backgroundColor: "black",
    color: "aqua",
    fontSize: 24, // 기본 단위 px
    padding: "1rem", // 다른 단위 사용 시 문자열로 설정
  };

  return (
    <>
      <Hello />
      <div style={style}>{name}</div>
    </>
  );
}

export default App;
```

CSS class 를 설정 할 때에는 class= 가 아닌 className= 으로 설정을 해야함.

```js
import React from "react";
import Hello from "./Hello";
import "./App.css";

function App() {
  const name = "react";
  const style = {
    backgroundColor: "black",
    color: "aqua",
    fontSize: 24, // 기본 단위 px
    padding: "1rem", // 다른 단위 사용 시 문자열로 설정
  };

  return (
    <>
      <Hello />
      <div style={style}>{name}</div>
      <div className="gray-box"></div>
    </>
  );
}

export default App;
```

### 주석

JSX 내부의 주석은 {/\* 이런 형태로 \*/} 작성.

열리는 태그 내부에서는 // 이런 형태로도 주석 작성이 가능.
