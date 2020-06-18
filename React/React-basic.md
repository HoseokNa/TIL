# React 기초

React 공부. 기본 내용 정리.

벨로퍼트와 함께하는 모던 리액트 참고. [링크](https://react.vlpt.us/)

# 목차

- [왜 React가 개발 됐을까](#왜-React가-개발-됐을까)
- [JSX](#JSX)
- [props](#props)
- [useState](#usetState)
- [useRef](#useRef)
- [배열 렌더링](#배열-렌더링)
- [배열 수정](#배열-수정)
- [useEffect](#useEffect)
- [useMemo](#useMemo)
- [useCallback](#useCallback)
- [React.memo](#React.memo)
- [useReducer](#useReducer)
- [커스텀 Hook](#커스텀-Hook)
- [Context API](#Context-API)
- [Immer](#immer)
- [클래스형 컴포넌트](#클래스형-컴포넌트)
- [LifeCycle Method](#LifeCycle-Method)

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

# props

## 어떠한 값을 컴포넌트에게 전달해줘야 할 때 props를 사용

### props 의 기본 사용법

예시) App 컴포넌트에서 Hello 컴포넌트를 사용 할 때 name 이라는 값을 전달할 때

```js
//App.js
import React from "react";
import Hello from "./Hello";

function App() {
  return <Hello name="react" />;
}

export default App;

// Hello.js
import React from "react";

function Hello(props) {
  return <div>안녕하세요 {props.name}</div>;
}

export default Hello;
```

### defaultProps 로 기본값 설정

컴포넌트에 props 를 지정하지 않았을 때 기본적으로 사용 할 값을 설정하고 싶다면 컴포넌트에 defaultProps 라는 값을 설정.

```js
import React from "react";

function Hello({ color, name }) {
  return <div style={{ color }}>안녕하세요 {name}</div>;
}

Hello.defaultProps = {
  name: "이름없음",
};

export default Hello;
```

### props.children

컴포넌트 태그 사이에 넣은 값을 조회하고 싶을 땐, props.children 을 조회

```js
// Wrapper.js
import React from "react";

function Wrapper({ children }) {
  const style = {
    border: "2px solid black",
    padding: "16px",
  };
  return <div style={style}>{children}</div>;
}

export default Wrapper;

// App.js
import React from "react";
import Hello from "./Hello";
import Wrapper from "./Wrapper";

function App() {
  return (
    <Wrapper>
      <Hello name="react" color="red" />
      <Hello color="pink" />
    </Wrapper>
  );
}

export default App;
```

### props 값 설정을 생략하면 ={true}

```js
import React from "react";
import Hello from "./Hello";
import Wrapper from "./Wrapper";

function App() {
  return (
    <Wrapper>
      <Hello name="react" color="red" isSpecial />
      <Hello color="pink" />
    </Wrapper>
  );
}

export default App;
```

이렇게 isSpecial 이름만 넣어주면 isSpecial={true} 와 동일한 의미.

# useState

## 함수형 컴포넌트에서 상태를 관리

리액트 16.8 에서 Hooks 라는 기능이 도입되면서 함수형 컴포넌트에서도 상태를 관리할 수 있게 됨.

컴포넌트에서 동적인 값을 상태(state)라고 부름.

리액트에 useState 라는 함수로 컴포넌트에서 상태를 관리.

```js
import React, { useState } from "react";

const [number, setNumber] = useState(0);
```

useState 를 사용 할 때에는 상태의 기본값을 파라미터로 넣어서 호출.

배열 형태로 반환. 첫번째 원소는 현재 상태, 두번째 원소는 Setter 함수.

```js
const onIncrease = () => {
  setNumber(number + 1);
};

const onDecrease = () => {
  setNumber(number - 1);
};
```

Setter 함수는 파라미터로 전달 받은 값을 최신 상태(여기서는 number)로 설정.

### 함수형 업데이트

지금은 Setter 함수를 사용 할 때, 업데이트 하고 싶은 새로운 값을 파라미터 넣어주고 있음.

기존 값을 어떻게 업데이트 할 지에 대한 함수를 등록하는 방식으로도 값을 업데이트 할 수 있음.

```js
const onIncrease = () => {
  setNumber((prevNumber) => prevNumber + 1);
};

const onDecrease = () => {
  setNumber((prevNumber) => prevNumber - 1);
};
```

함수형 업데이트는 주로 나중에 컴포넌트를 최적화를 하게 될 때 사용하게 됨.

### 여러개의 input 상태 관리

단순히 useState 를 여러번 사용하고 onChange 도 여러개 만들어서 구현 할 수 있음. 좋은 방법 아님.

input 에 name 을 설정하고 이벤트가 발생했을 때 이 값을 참조.

useState 에서는 문자열이 아니라 객체 형태의 상태를 관리.

```js
import React, { useState } from "react";

function InputSample() {
  const [inputs, setInputs] = useState({
    name: "",
    nickname: "",
  });

  const { name, nickname } = inputs; // 비구조화 할당을 통해 값 추출

  const onChange = (e) => {
    const { value, name } = e.target; // 우선 e.target 에서 name 과 value 를 추출
    setInputs({
      ...inputs, // 기존의 input 객체를 복사한 뒤
      [name]: value, // name 키를 가진 값을 value 로 설정
    });
  };

  const onReset = () => {
    setInputs({
      name: "",
      nickname: "",
    });
  };

  return (
    <div>
      <input name="name" placeholder="이름" onChange={onChange} value={name} />
      <input
        name="nickname"
        placeholder="닉네임"
        onChange={onChange}
        value={nickname}
      />
      <button onClick={onReset}>초기화</button>
      <div>
        <b>값: </b>
        {name} ({nickname})
      </div>
    </div>
  );
}

export default InputSample;
```

### 불변성 지키기

불변성을 지켜주어야만 리액트 컴포넌트에서 상태가 업데이트가 됐음을 감지 할 수 있고 이에 따라 필요한 리렌더링이 진행.

리액트에서 객체를 업데이트하게 될 때에는 기존 객체를 직접 수정하면 안되고, 새로운 객체를 만들어서, 새 객체에 변화를 주어야함.

```js
// X
inputs[name] = value;

// Spread 문법 사용으로 기존 객체 복사
setInputs({
  ...inputs,
  [name]: value,
});
```

# useRef

## 1. useRef 로 특정 DOM 선택하기

## 2. 컴포넌트 안에서 조회 및 수정 할 수 있는 변수를 관리

리액트에서 DOM을 직접 선택할 때 ref 사용.

함수형 컴포넌트에서 useRef 라는 Hook 함수를 사용.

useRef() 를 사용하여 Ref 객체를 만듬. 그리고 이 객체를 우리가 선택하고 싶은 DOM 에 ref 값으로 설정. 그러면, Ref 객체의 .current 값은 우리가 원하는 DOM 을 가르킴.

```js
import React, { useState, useRef } from "react";

function InputSample() {
  const [inputs, setInputs] = useState({
    name: "",
    nickname: "",
  });
  const nameInput = useRef();

  const { name, nickname } = inputs; // 비구조화 할당을 통해 값 추출

  const onReset = () => {
    setInputs({
      name: "",
      nickname: "",
    });
    nameInput.current.focus();
  };

  return (
    <div>
      <input
        name="name"
        placeholder="이름"
        onChange={onChange}
        value={name}
        ref={nameInput}
      />
    </div>
  );
}

export default InputSample;
```

useRef 로 관리하는 변수는 값이 바뀐다고 해서 컴포넌트가 리렌더링되지 않음.

다음과 같은 값을 관리 할 수 있음.

- setTimeout, setInterval 을 통해서 만들어진 id.
- 외부 라이브러리를 사용하여 생성된 인스턴스.
- scroll 위치.

```js
import React, { useRef } from "react";
import UserList from "./UserList";

function App() {
  const users = [
    {
      id: 1,
      username: "velopert",
      email: "public.velopert@gmail.com",
    },
  ];

  const nextId = useRef(4);
  const onCreate = () => {
    // 나중에 구현 할 배열에 항목 추가하는 로직
    // ...

    nextId.current += 1;
  };
  return <UserList users={users} />;
}

export default App;
```

useRef() 를 사용 할 때 파라미터를 넣어주면, 이 값이 .current 값의 기본값이 됨.

이 값을 수정 할때에는 .current 값을 수정. 조회 할 때에는 .current 를 조회.

# 배열 렌더링

## key props를 설정해서 렌더링 하기

map 사용하기

각 고유 원소에 key 가 있어야만 배열이 업데이트 될 때 효율적으로 렌더링 됨.

원소 변경 시 key가 없을 경우 순차적으로 다 변경하여 리렌더링. key가 있을 경우 해당 위치에서만 리렌더링

```js
import React from "react";

function User({ user }) {
  return (
    <div>
      <b>{user.username}</b> <span>({user.email})</span>
    </div>
  );
}

function UserList() {
  const users = [
    {
      id: 1,
      username: "velopert",
      email: "public.velopert@gmail.com",
    },
    {
      id: 2,
      username: "tester",
      email: "tester@example.com",
    },
    {
      id: 3,
      username: "liz",
      email: "liz@example.com",
    },
  ];

  return (
    <div>
      {users.map((user) => (
        <User user={user} key={user.id} />
      ))}
    </div>
  );
}

export default UserList;
```

# 배열 수정

## spread 연산, concat, filter, map 사용하기

불변성을 지켜주기.

push, splice, sort 등을 사용하려면 기존의 배열을 복사하고 사용하자.

```js
// App.js
import React, { useRef, useState } from "react";
import UserList from "./UserList";
import CreateUser from "./CreateUser";

function App() {
  const [inputs, setInputs] = useState({
    username: "",
    email: "",
  });
  const { username, email } = inputs;
  const onChange = (e) => {
    const { name, value } = e.target;
    setInputs({
      ...inputs,
      [name]: value,
    });
  };
  const [users, setUsers] = useState([
    {
      id: 1,
      username: "velopert",
      email: "public.velopert@gmail.com",
      active: true,
    },
    {
      id: 2,
      username: "tester",
      email: "tester@example.com",
      active: false,
    },
    {
      id: 3,
      username: "liz",
      email: "liz@example.com",
      active: false,
    },
  ]);

  const nextId = useRef(4);
  const onCreate = () => {
    const user = {
      id: nextId.current,
      username,
      email,
    };
    setUsers(users.concat(user));

    setInputs({
      username: "",
      email: "",
    });
    nextId.current += 1;
  };

  const onRemove = (id) => {
    // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
    // = user.id 가 id 인 것을 제거함
    setUsers(users.filter((user) => user.id !== id));
  };
  const onToggle = (id) => {
    setUsers(
      users.map((user) =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  };
  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onRemove={onRemove} onToggle={onToggle} />
    </>
  );
}

export default App;

// UserList.js
import React from "react";

function User({ user, onRemove, onToggle }) {
  return (
    <div>
      <b
        style={{
          cursor: "pointer",
          color: user.active ? "green" : "black",
        }}
        onClick={() => onToggle(user.id)}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button onClick={() => onRemove(user.id)}>삭제</button>
    </div>
  );
}

function UserList({ users, onRemove, onToggle }) {
  return (
    <div>
      {users.map((user) => (
        <User
          user={user}
          key={user.id}
          onRemove={onRemove}
          onToggle={onToggle}
        />
      ))}
    </div>
  );
}

export default UserList;
```

# useEffect

## 리액트에게 컴포넌트가 렌더링 이후에 어떤 일을 수행해야하는지 말해줌.

```js
function useEffect(effect: EffectCallback, inputs?: InputIdentityList)
```

render가 발생할 때 마다(componentDidMount: 초기, componentDUpdate: 매번) effect가 실행. 두 번째 파라미터인 inputs를 통해 특정한 상태가 update 되었을 때만 effect가 실행되도록 설정(의존값).

useEffect Hook을 사용하여 컴포넌트가 마운트 됐을 때 (처음 나타났을 때), 언마운트 됐을 때 (사라질 때), 그리고 업데이트 될 때 (특정 props가 바뀔 때) 특정 작업을 처리할 수 있음.

### 마운트 / 언마운트

```js
useEffect(() => {
  console.log("컴포넌트가 화면에 나타남");
  return () => {
    console.log("컴포넌트가 화면에서 사라짐");
  };
}, []);
```

두 번째 파라미터 배열(deps)을 비워두면 컴포넌트가 처음 나타날 때에만 등록한 함수 호출.

useEffect 에서는 함수를 반환 할 수 있는데 이를 cleanup 함수라고 함. deps 가 비어있는 경우에는 컴포넌트가 사라질 때 cleanup 함수가 호출.

마운트시 주로 하는 작업

- props 로 받은 값을 컴포넌트의 로컬 상태로 설정.
- 외부 API 요청 (REST API 등).
- 라이브러리 사용 (D3, Video.js 등...).
- setInterval 을 통한 반복작업 혹은 setTimeout 을 통한 작업 예약.

언마운트 시에 하는 작업

- setInterval, setTimeout 을 사용하여 등록한 작업들 clear 하기 (clearInterval, clearTimeout).
- 라이브러리 인스턴스 제거.

### deps 에 특정 값 넣기

deps 에 특정 값을 넣게 된다면,

- 컴포넌트가 처음 마운트 될 때에도 호출이 되고, 지정한 값이 바뀔 때에도 호출.
- 언마운트시에도 호출이되고, 값이 바뀌기 직전에도 호출.

```js
useEffect(() => {
  console.log("user 값이 설정됨");
  console.log(user);
  return () => {
    console.log("user 가 바뀌기 전..");
    console.log(user);
  };
}, [user]);
```

**useEffect 안에서 사용하는 상태나, props 가 있다면, useEffect 의 deps 에 넣어야 함.**

그렇지 않으면 useEffect 에 등록한 함수가 실행 될 때 최신 props / 상태를 가르키지 않음.

### deps 파라미터를 생략하기

deps 파라미터를 생략한다면, 컴포넌트가 리렌더링 될 때마다 호출.

```js
useEffect(() => {
  console.log(user);
});
```

참고로 리액트 컴포넌트는 기본적으로 부모컴포넌트가 리렌더링되면 자식 컴포넌트 또한 리렌더링이 됨.(바뀐 내용이 없어도)

물론, 실제 DOM 에 변화가 반영되는 것은 바뀐 내용이 있는 컴포넌트에만 해당.

# useMemo

## 성능 최적화를 위하여 연산된 값을 재사용하는 Hook

첫번째 파라미터에는 어떻게 연산할지 정의하는 함수.

두번째 파라미터에는 deps 배열. 이 배열 안에 넣은 내용이 바뀌면, 우리가 등록한 함수를 호출해서 값을 연산. 바뀌지 않았다면 이전에 연산한 값을 재사용.

```js
function countActiveUsers(users) {
  console.log("활성 사용자 수를 세는중...");
  return users.filter((user) => user.active).length;
}

const count = useMemo(() => countActiveUsers(users), [users]);
```

# useCallback

## 특정 함수를 새로 만들지 않고 재사용하고 싶을때 사용

최적화 작업을 위해서 필수적.

컴포넌트에서 props 가 바뀌지 않았으면 Virtual DOM 에 새로 렌더링하는 것 조차 하지 않고 컴포넌트의 결과물을 재사용 가능.

**함수 안에서 사용하는 상태 혹은 props 가 있다면 꼭, deps 배열안에 포함시켜야 함.**

그렇지 않으면 함수 내에서 해당 값들을 참조할때 가장 최신 값을 참조 할 것이라고 보장 못함. props 로 받아온 함수가 있다면, 이 또한 deps 에 넣어야 함.

```js
const onChange = useCallback(
  (e) => {
    const { name, value } = e.target;
    setInputs({
      ...inputs,
      [name]: value,
    });
  },
  [inputs]
);
const onCreate = useCallback(() => {
  const user = {
    id: nextId.current,
    username,
    email,
  };
  setUsers(users.concat(user));

  setInputs({
    username: "",
    email: "",
  });
  nextId.current += 1;
}, [users, username, email]);

const onRemove = useCallback(
  (id) => {
    // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
    // = user.id 가 id 인 것을 제거함
    setUsers(users.filter((user) => user.id !== id));
  },
  [users]
);
const onToggle = useCallback(
  (id) => {
    setUsers(
      users.map((user) =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  },
  [users]
);
```

사실 useCallback 을 사용 함으로써, 바로 이뤄낼수 있는 눈에 띄는 최적화는 없음. 컴포넌트 렌더링 최적화 작업을 해주어야만 성능이 최적화됨.

# React.memo

## 컴포넌트의 props 가 바뀌지 않았다면, 리렌더링을 방지하는 함수

Props가 바뀌었을때만 리렌더링. 렌더링된 결과물을 재사용.

```js
// CreateUser.js
export default React.memo(CreateUser);

// UserList.js
export default React.memo(UserList);
```

하지만 기존 예제에서 User 중 하나라도 수정하면 모든 User 들이 리렌더링되고, CreateUser 도 리렌더링 됨.

users 배열이 바뀔때마다 onCreate, onToggle,onRemove이 새로 만들어지기 때문.(useCallback에서 deps가 users)

useState를 함수형 업데이트로 수정하고 기존 useCallback deps의 users를 삭제하면 해결.

```js
const onChange = useCallback((e) => {
  const { name, value } = e.target;
  setInputs((inputs) => ({
    ...inputs,
    [name]: value,
  }));
}, []);

const onCreate = useCallback(() => {
  const user = {
    id: nextId.current,
    username,
    email,
  };
  setUsers((users) => users.concat(user));

  setInputs({
    username: "",
    email: "",
  });
  nextId.current += 1;
}, [username, email]);

const onRemove = useCallback((id) => {
  // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
  // = user.id 가 id 인 것을 제거함
  setUsers((users) => users.filter((user) => user.id !== id));
}, []);
const onToggle = useCallback((id) => {
  setUsers((users) =>
    users.map((user) =>
      user.id === id ? { ...user, active: !user.active } : user
    )
  );
}, []);
```

useCallback, useMemo, React.memo 는 컴포넌트의 성능을 실제로 개선할수있는 상황에서만 사용.

b 와 button 에 onClick 으로 설정해준 함수들은, useCallback 으로 재사용한다고 해서 리렌더링을 막을 수 없음.

렌더링 최적화 하지 않을 컴포넌트에 React.memo 를 사용하는것은, 불필요한 props 비교만 하는 것.

React.memo 에서 두번째 파라미터에 propsAreEqual 이라는 함수를 사용하여 특정 값들만 비교를 하는 것도 가능. 하지만, 이걸 잘못사용한다면 오히려 의도치 않은 버그들이 발생.

# useReducer

## 컴포넌트의 상태 업데이트 로직을 컴포넌트에서 분리시킬 수 있는 Hook

useState 대신 useReducer를 사용하여 컴포넌트의 상태 업데이트 가능.

컴포넌트 바깥에 작성 할 수도 있고, 심지어 다른 파일에 작성 후 불러와서 사용 할 수 있음.

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

state 는 우리가 앞으로 컴포넌트에서 사용 할 수 있는 상태.

distpatch 는 액션을 발생시키는 함수.

첫번째 파라미터는 reducer 함수, 두번째 파라미터는 초기 상태.

reducer 는 현재 상태와 액션 객체를 파라미터로 받아와서 새로운 상태를 반환해주는 함수.

```js
function reducer(state, action) {
  // 새로운 상태를 만드는 로직
  // const nextState = ...
  return nextState;
}
```

action 은 업데이트를 위한 정보. action 객체의 형태는 자유.

Counter 컴포넌트의 useState를 useReducer로 구현한 예제.

```js
// useState 사용시

function Counter() {
  const [number, setNumber] = useState(0);

  const onIncrease = () => {
    setNumber((prevNumber) => prevNumber + 1);
  };

  const onDecrease = () => {
    setNumber((prevNumber) => prevNumber - 1);
  };

  // ...
}

// 변경 후
function reducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    case "DECREMENT":
      return state - 1;
    default:
      return state;
  }
}

function Counter() {
  const [number, dispatch] = useReducer(reducer, 0);

  const onIncrease = () => {
    dispatch({ type: "INCREMENT" });
  };

  const onDecrease = () => {
    dispatch({ type: "DECREMENT" });
  };

  //...
}
```

### useReducer vs useState 뭐가 좋나?

정해진 답은 없음.

컴포넌트에서 관리하는 값이 딱 하나고, 그 값이 단순한 숫자, 문자열 또는 boolean 값이라면 확실히 useState 로 관리하는게 편할 듯.

만약에 컴포넌트에서 관리하는 값이 여러개가 되어서 상태의 구조가 복잡해진다면 useReducer로 관리하는 것이 편할 수 있음.

setter 를 한 함수에서 여러번 사용해야 하는 일이 발생한다면, 그 때부터 useReducer 를 쓸까? 에 대한 고민을 시작한다고 함.

자주 사용해보시고 맘에드는 방식을 선택하자.

# 커스텀 Hook

## 반복되는 로직을 쉽게 재사용 가능.

커스텀 hook을 만들면 컴포넌트 로직을 재사용 가능한 함수로 뽑아낼 수 있음.

커스텀 Hooks 를 만들 때에는 보통 use 라는 키워드로 시작하는 파일을 만들고 그 안에 함수를 작성.

그 안에서 useState, useEffect, useReducer, useCallback 등 Hooks 를 사용하여 원하는 기능을 구현.

컴포넌트에서 사용하고 싶은 값들을 반환.

```js
// useInputs.js

import { useState, useCallback } from "react";

function useInputs(initialForm) {
  const [form, setForm] = useState(initialForm);
  // change
  const onChange = useCallback((e) => {
    const { name, value } = e.target;
    setForm((form) => ({ ...form, [name]: value }));
  }, []);
  const reset = useCallback(() => setForm(initialForm), [initialForm]);
  return [form, onChange, reset];
}

export default useInputs;
```

# Context API

## 프로젝트 안에서 전역적으로 사용 할 수 있는 값을 관리

특정 함수를 3~4개 이상의 컴포넌트를 거쳐서 전달을 해야 하는 일은 번거로움.

리액트의 Context API 를 사용하면, 프로젝트 안에서 전역적으로 사용 할 수 있는 값을 관리 가능.

이 값은 함수일수도 있고, 어떤 외부 라이브러리 인스턴스일수도 있고 심지어 DOM 일 수도 있음.

React.createContext()로 생성

```js
const UserDispatch = React.createContext(null);
```

파라미터에는 Context 의 기본값을 설정.

Context 안에 Provider 컴포넌트로 Context의 값을 정할 수 있음.

```js
// Context 안에 값을 dispatch로 설정
<UserDispatch.Provider value={dispatch}>...</UserDispatch.Provider>
```

Provider 에 의하여 감싸진 컴포넌트 중 어디서든지 우리가 Context 의 값을 바로 조회하여 사용 가능.

조회시에는 useContext 사용.

```js
// App.js

// ...

export const UserDispatch = React.createContext(null);

function App() {
  // ...

  const [state, dispatch] = useReducer(reducer, initialState);

  // ...
  return (
    <UserDispatch.Provider value={dispatch}>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} />
      <div>활성사용자 수 : {count}</div>
    </UserDispatch.Provider>
  );
}

// User.js
import React, { useContext } from "react";
import { UserDispatch } from "./App";

const User = React.memo(function User({ user }) {
  const dispatch = useContext(UserDispatch);

  return (
    <div>
      <b
        style={{
          cursor: "pointer",
          color: user.active ? "green" : "black",
        }}
        onClick={() => {
          dispatch({ type: "TOGGLE_USER", id: user.id });
        }}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button
        onClick={() => {
          dispatch({ type: "REMOVE_USER", id: user.id });
        }}
      >
        삭제
      </button>
    </div>
  );
});

function UserList({ users }) {
  return (
    <div>
      {users.map((user) => (
        <User user={user} key={user.id} />
      ))}
    </div>
  );
}

export default React.memo(UserList);
```

컴포넌트에게 함수를 전달해줘야 할 때 Context API로 dispatch를 전역적으로 사용할 수 있어서 코드가 훨씬 깔끔해 질 수 있음. (useState와 useReducer의 차이)

# Immer

## 상태를 업데이트 할 때 불변성을 관리 해주는 라이브러리

데이터의 구조가 복잡해지면 불변성을 지켜주면서 업데이트를 하려면 코드가 복잡해짐.

immer 라이브러리를 사용하면 불변성을 신경쓰지 않고 업데이트가 가능.

### 사용 법

immer 설치

```js
$ yarn add immer
```

produce import

```js
import produce from "immer";
```

produce 함수를 사용 할 때에는 첫번째 파라미터에는 수정하고 싶은 상태.

두번째 파라미터에는 어떻게 업데이트하고 싶을지 정의하는 함수. (불변성에 대해서 신경 안써도 됨)

```js
const state = {
  number: 1,
  dontChangeMe: 2,
};

const nextState = produce(state, (draft) => {
  draft.number += 1;
});

console.log(nextState);
// { number: 2, dontChangeMe: 2 }
```

immer 를 사용해서 간단해지는 업데이트가 있고, 오히려 코드가 길어지는 업데이트가 있음

새 항목을 추가하거나 제거 할 때는 Immer 를 사용하는 것 보다 concat 과 filter 를 사용하는것이 더 코드가 짧고 편함.

### Immer와 함수형 업데이트

produce 함수에 첫번째 파라미터를 생략하고 바로 업데이트 함수를 넣어주게 된다면, 반환 값은 새로운 상태가 아닌 상태를 업데이트 해주는 함수가 됨.

```js
const todo = {
  text: "Hello",
  done: false,
};

const updater = produce((draft) => {
  draft.done = !draft.done;
});

const nextTodo = updater(todo);

console.log(nextTodo);
// { text: 'Hello', done: true }
```

useState의 업데이트 함수를 사용할 때 다음과 같이 구현 가능.

```js
const [todo, setTodo] = useState({
  text: "Hello",
  done: false,
});

const onClick = useCallback(() => {
  setTodo(
    produce((draft) => {
      draft.done = !draft.done;
    })
  );
}, []);
```

편한 라이브러리인것은 사실이지만, 성능적으로는 Immer 를 사용하지 않은 코드가 조금 더 빠름. (데이터가 적으면 거의 차이가 없음)

Immer 를 사용한다고 해도, 필요한곳에만 쓰고, 간단히 처리 될 수 있는 곳에서는 그냥 일반 JavaScript 로 구현하자.

# 클래스형 컴포넌트

클래스형 컴포넌트 작성법

```js
import React, { Component } from "react";

class Hello extends Component {
  // static을 사용해서 defaultProps 설정 가능
  // static defaultProps = {
  //   name: '이름없음'
  // };

  render() {
    const { color, name, isSpecial } = this.props;
    return (
      <div style={{ color }}>
        {isSpecial && <b>*</b>}
        안녕하세요 {name}
      </div>
    );
  }
}

Hello.defaultProps = {
  name: "이름없음",
};

export default Hello;
```

render() 메서드가 꼭 있어야 함. 이 메서드에서 렌더링하고 싶은 JSX 를 반환.

this.props로 props 조회.

### 커스텀 메서드

```js
import React, { Component } from "react";

class Counter extends Component {
  handleIncrease() {
    console.log("increase");
    console.log(this);
  }

  handleDecrease() {
    console.log("decrease");
  }

  render() {
    return (
      <div>
        <h1>0</h1>
        <button onClick={this.handleIncrease}>+1</button>
        <button onClick={this.handleDecrease}>-1</button>
      </div>
    );
  }
}

export default Counter;
```

일반적으로 클래스 안에 커스텀 메서드를 선언하여 사용.

handleIncrease()의 this를 출력하면 undefined 출력.

메서드들을 각 button 들의 이벤트로 등록하게 되는 과정에서 각 메서드와 컴포넌트 인스턴스의 관계가 끊겨기 때문.

해결 방법 3가지.

1. 생성자 메서드 constructor 에서 bind

```js
class Counter extends Component {
  constructor(props) {
    super(props);
    this.handleIncrease = this.handleIncrease.bind(this);
    this.handleDecrease = this.handleDecrease.bind(this);
  }

  // ...
}
```

2.  커스텀 매서드 선언 시 화살표 문법 사용. (보통 CRA 로 만든 프로젝트에서는 커스텀 메서드를 만들 때 이 방법을 많이 사용)

```js
class Counter extends Component {
  handleIncrease = () => {
    console.log("increase");
    console.log(this);
  };

  handleDecrease = () => {
    console.log("decrease");
  };

  // ...
}
```

3. onClick 에서 새로운 함수를 만들어서 전달. (비추. 렌더링 할 때마다 새로 만들어지기 때문에 최적화 어려움)

```js
// ...

return (
  <div>
    <h1>0</h1>
    <button onClick={() => this.handleIncrease()}>+1</button>
    <button onClick={() => this.handleDecrease()}>-1</button>
  </div>
);
```

### 상태 선언하기

상태 관리시 state 사용.

constructor 내부에서 this.state 를 설정.

state 는 무조건 객체형태.

this.state로 조회.

```js
import React, { Component } from "react";

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = {
      counter: 0,
    };
  }

  //...
}
```

화살표 함수 문법을 사용하여 메서드를 작성 할 수 있게 해줬던 class-properties 문법이 적용되어 있다면 굳이 constructor 를 작성하지 않고도 다음과 같이 state 를 설정 가능.

```js
class Counter extends Component {
  state = {
    counter: 0,
  };
  // ...
}
```

### 상태 업데이트하기

this.setState 함수를 사용.

```js
import React, { Component } from "react";

class Counter extends Component {
  state = {
    counter: 0,
  };
  handleIncrease = () => {
    this.setState({
      counter: this.state.counter + 1,
    });
  };

  handleDecrease = () => {
    this.setState({
      counter: this.state.counter - 1,
    });
  };

  render() {
    return (
      <div>
        <h1>{this.state.counter}</h1>
        <button onClick={this.handleIncrease}>+1</button>
        <button onClick={this.handleDecrease}>-1</button>
      </div>
    );
  }
}

export default Counter;
```

### setState 의 함수형 업데이트

useState 에서 함수형 업데이트를 할 수 있었던 것 처럼 setState 도 마찬가지로 함수형 업데이트 가능.

함수형 업데이트는 보통 한 함수에서 setState 를 여러번에 걸쳐서 해야 되는 경우에 사용하면 유용.

아래 예제는 실제로 2가 더해지지 않음.

```js
handleIncrease = () => {
  this.setState({
    counter: this.state.counter + 1,
  });
  this.setState({
    counter: this.state.counter + 1,
  });
};
```

함수형 업데이트로 처리하면 +2 됨.

```js
handleIncrease = () => {
  this.setState((state) => ({
    counter: state.counter + 1,
  }));
  this.setState((state) => ({
    counter: state.counter + 1,
  }));
};
```

업데이트 할 객체를 넣어주는 setState 에서 2씩 더해지지 않는 이유는 setState 를 한다고 해서 상태가 바로 바뀌는게 아니기 때문.

setState 는 단순히 상태를 바꾸는 함수가 아니라 상태로 바꿔달라고 요청해주는 함수로 이해를 해야함.

만약에, 상태가 업데이트 되고 나서 어떤 작업을 하고 싶다면 다음과 같이 setState 의 두번째 파라미터에 콜백함수를 넣어줄 수도 있음.

# LifeCycle Method

## 컴포넌트가 브라우저상에 나타나고, 업데이트되고, 사라지게 될 때 호출되는 메서드들

![lifecycle_methods](./img/lifecycle_methods.png)

> 출처: http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/

## 마운트

마운트 될 때 발생하는 생명주기들.

- constructor
- getDerivedStateFromProps
- render
- componentDidMount

### constructor

컴포넌트의 생성자 메서드. 컴포넌트가 만들어지면 가장 먼저 실행되는 메서드.

### getDerivedStateFromProps

props 로 받아온 것을 state 에 넣어주고 싶을 때 사용.

앞에 static 을 필요.

this 롤 조회 할 수 없음.

특정 객체를 반환하게 되면 해당 객체 안에 있는 내용들이 컴포넌트의 state 로 설정.

### render

컴포넌트를 렌더링하는 메서드.

### componentDidMount

컴포넌트의 첫번째 렌더링이 마치고 나면 호출되는 메서드.

DOM 을 사용해야하는 외부 라이브러리 연동, ajax 요청, DOM 의 속성을 읽거나 직접 변경하는 작업을 진행.

## 업데이트

컴포넌트가 업데이트 되는 시점에 생명주기들.

- getDerivedStateFromProps
- shouldComponentUpdate
- render
- getSnapshotBeforeUpdate
- componentDidUpdate

### getDerivedStateFromProps

컴포넌트의 props 나 state 가 바뀌었을때도 이 메서드가 호출.

### shouldComponentUpdate

컴포넌트가 리렌더링 할지 말지를 결정하는 메서드.

주로 최적화 할 때 사용하는 메서드. React.memo와 비슷한 역할.

### render

### getSnapshotBeforeUpdate

컴포넌트에 변화가 일어나기 직전의 DOM 상태를 가져와서 특정 값을 반환하면 그 다음 발생하게 되는 componentDidUpdate 함수에서 받아와서 사용 가능.

DOM 에 변화가 반영되기 직전에 DOM 의 속성을 확인하고 싶을 때 사용.

### componentDidUpdate

리렌더링이 마치고, 화면에 우리가 원하는 변화가 모두 반영되고 난 뒤 호출되는 메서드.

3번째 파라미터로 getSnapshotBeforeUpdate 에서 반환한 값을 조회 할 수 있음.

## 언마운트

컴포넌트가 화면에서 사라지는것을 의미. componentWillUnmount 하나.

### componentWillUnmount

컴포넌트가 화면에서 사라지기 직전에 호출.

주로 DOM에 직접 등록했었던 이벤트를 제거하고, 만약에 setTimeout 을 걸은것이 있다면 clearTimeout 을 통하여 제거.

외부 라이브러리를 사용한게 있고 해당 라이브러리에 dispose 기능이 있다면 여기서 호출.
