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
