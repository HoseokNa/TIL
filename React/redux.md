# Redux

벨로퍼트와 함께하는 모던 리액트 참고. [링크](https://react.vlpt.us/redux/)

리덕스는 리액트 생태계에서 가장 사용률이 높은 상태관리 라이브러리.

# 목차

- [리덕스와 Context API 차이](#리덕스와-Context-API-차이)
- [리덕스에서 사용되는 키워드 숙지하기](#리덕스에서-사용되는-키워드-숙지하기)
- [리덕스의 3가지 규칙](#리덕스의-3가지-규칙)
- [리덕스 사용할 준비하기](#리덕스-사용할-준비하기)
- [리덕스 모듈](#리덕스-모듈)
- [카운터 구현하기](#카운터-구현하기)
- [useSelector 최적화](#useSelector-최적화)

# 리덕스와 Context API 차이

Context API 를 사용해도 글로벌 상태 관리를 할 수 있고 상태 관리 로직을 분리 할 수 있음.

그렇다면 리덕스를 사용하는 것 과 Context API를 사용하는 것은 어떠한 차이가 있을까?

### 1. 미들웨어

리덕스의 미들웨어를 사용하면 액션 객체가 리듀서에서 처리되기 전에 우리가 원하는 작업들을 수행 할 수 있음.

미들웨어는 주로 비동기 작업을 처리 할 때 많이 사용.

### 2. 유용한 함수와, Hooks

`connect` 함수를 사용하면 리덕스의 상태 또는 액션 생성 함수를 컴포넌트의 props 로 받아올 수 있음. `useSelector`, `useDispatch`, `useStore` 과 같은 Hooks를 사용하면 손쉽게 상태를 조회하거나 액션을 디스패치 할 수도 있음.

`connect` 함수와 `useSelector` 함수에는 내부적으로 최적화가 잘 이루어져있어서 실제 상태가 바뀔때만 컴포넌트가 리렌더링됨.

### 3. 하나의 커다란 상태

리덕스에서는 모든 글로벌 상태를 하나의 커다란 상태 객체에 넣어서 사용하는 것이 필수.

매번 Context를 새로 만드는 수고로움을 덜 수 있음.

### 그럼 리덕스는 언제 써야 할까?

다음 사항들을 고려.

### 1. 프로젝트의 규모가 큰가?

- Yes: 리덕스
- No: Context API

### 2. 비동기 작업을 자주 하게 되는가?

- Yes: 리덕스
- No: Context API

### 3. 리덕스를 배워보니까 사용하는게 편한가?

- Yes: 리덕스
- No: Context API 또는 MobX

# 리덕스에서 사용되는 키워드 숙지하기

## 액션 (Action)

상태에 어떠한 변화가 필요하게 될 땐, 우리는 액션이란 것을 발생.

하나의 객체로 표현.

액션 객체는 type 필드를 필수적으로 가지고 있어야하고 그 외의 값들은 개발자 마음대로 넣어줄 수 있음.

```js
{
  type: "ADD_TODO",
  data: {
    id: 0,
    text: "리덕스 배우기"
  }
}
```

## 액션 생성함수 (Action Creator)

액션을 만드는 함수.

단순히 파라미터를 받아와서 액션 객체 형태로 만듬.

```js
export function addTodo(data) {
  return {
    type: "ADD_TODO",
    data,
  };
}

// 화살표 함수로도 만들 수 있습니다.
export const changeInput = (text) => ({
  type: "CHANGE_INPUT",
  text,
});
```

나중에 컴포넌트에서 더욱 쉽게 액션을 발생시키기 위함. 그래서 보통 함수 앞에 export 키워드를 붙여서 다른 파일에서 불러와서 사용.

## 리듀서 (Reducer)

변화를 일으키는 함수.

현재의 상태와, 전달 받은 액션을 참고하여 새로운 상태를 만들어서 반환.(`useReducer`를 사용할 때 작성하는 리듀서와 똑같은 형태)

```js
function counter(state, action) {
  switch (action.type) {
    case "INCREASE":
      return state + 1;
    case "DECREASE":
      return state - 1;
    default:
      return state;
  }
}
```

`useReducer` 에선 일반적으로 default: 부분에 throw new Error('Unhandled Action')과 같이 에러를 발생시키도록 처리.

리덕스의 리듀서에서는 기존 state를 그대로 반환하도록 작성.

리덕스를 사용 할 때에는 여러개의 리듀서(서브 리듀서)를 만들고 이를 합쳐서 루트 리듀서 (Root Reducer)를 만들 수 있음.

## 스토어 (Store)

리덕스에서는 한 애플리케이션당 하나의 스토어를 만들어야함.

스토어 안에는, 현재의 앱 상태와, 리듀서가 들어가있고, 추가적으로 몇가지 내장 함수들이 있음.

## 디스패치 (dispatch)

디스패치는 스토어의 내장함수 중 하나.

디스패치는 액션을 발생 시키는 것. dispatch 라는 함수에는 액션을 파라미터로 전달. (`dispatch(action)`)

그렇게 호출을 하면, 스토어는 리듀서 함수를 실행시켜서 해당 액션을 처리하는 로직이 있다면 액션을 참고하여 새로운 상태를 만들어줌.

## 구독 (subscribe)

스토어의 내장함수 중 하나.

함수 형태의 값을 파라미터로 받아옴.

subscribe 함수에 특정 함수를 전달해주면, 액션이 디스패치 되었을 때 마다 전달해준 함수가 호출.

리액트에서 리덕스를 사용하게 될 때 보통 이 함수를 직접 사용하는 일은 별로 없음. 그 대신에 react-redux 라는 라이브러리에서 제공하는 `connect` 함수 또는 `useSelector` Hook 을 사용하여 리덕스 스토어의 상태에 구독함.

# 리덕스의 3가지 규칙

## 1. 하나의 애플리케이션 안에는 하나의 스토어.

여러개의 스토어를 사용하는것은 사실 가능하기는 하나, 권장되지는 않음.

## 2. 상태는 읽기전용

기존의 상태는 건들이지 않고 새로운 상태를 생성하여 업데이트 해주는 방식.

이렇게 하면 나중에 개발자 도구를 통해서 뒤로 돌릴 수도 있고 다시 앞으로 돌릴 수도 있음.

불변성을 유지하면 `shallow equality` 검사를 함. 이를 통해 겉핥기 식으로 비교를 하여 좋은 성능을 유지 가능.

## 3. 리듀서는 순수한 함수여야 함

순수한 함수?

- 리듀서 함수는 이전 상태와, 액션 객체를 파라미터로 받음.
- 이전의 상태는 절대로 건들이지 않고, 변화를 일으킨 새로운 상태 객체를 만들어서 반환.
- 똑같은 파라미터로 호출된 리듀서 함수는 언제나 똑같은 결과값을 반환해야만 함.

**동일한 인풋이라면 언제나 동일한 아웃풋이 되야함.**

new Date(), 랜덤 숫자를 생성 혹은, 네트워크에 요청 같은 작업은 리듀서 바깥에서 처리. 그런 작업을 위해 리덕스 미들웨어를 사용.

# 리덕스-사용할-준비하기

## 리액트 컴포넌트 없이, 리덕스에서 제공되는 기능들을 먼저 연습하기

```js
import { createStore } from "redux";

// createStore는 스토어를 만들어주는 함수입니다.
// 리액트 프로젝트에서는 단 하나의 스토어를 만듭니다.

/* 리덕스에서 관리 할 상태 정의 */
const initialState = {
  counter: 0,
  text: "",
  list: [],
};

/* 액션 타입 정의 */
// 액션 타입은 주로 대문자로 작성합니다.
const INCREASE = "INCREASE";
const DECREASE = "DECREASE";
const CHANGE_TEXT = "CHANGE_TEXT";
const ADD_TO_LIST = "ADD_TO_LIST";

/* 액션 생성함수 정의 */
// 액션 생성함수는 주로 camelCase 로 작성합니다.
function increase() {
  return {
    type: INCREASE, // 액션 객체에는 type 값이 필수입니다.
  };
}

// 화살표 함수로 작성하는 것이 더욱 코드가 간단하기에,
// 이렇게 쓰는 것을 추천합니다.
const decrease = () => ({
  type: DECREASE,
});

const changeText = (text) => ({
  type: CHANGE_TEXT,
  text, // 액션안에는 type 외에 추가적인 필드를 마음대로 넣을 수 있습니다.
});

const addToList = (item) => ({
  type: ADD_TO_LIST,
  item,
});

/* 리듀서 만들기 */
// 위 액션 생성함수들을 통해 만들어진 객체들을 참조하여
// 새로운 상태를 만드는 함수를 만들어봅시다.
// 주의: 리듀서에서는 불변성을 꼭 지켜줘야 합니다!

function reducer(state = initialState, action) {
  // state 의 초깃값을 initialState 로 지정했습니다.
  switch (action.type) {
    case INCREASE:
      return {
        ...state,
        counter: state.counter + 1,
      };
    case DECREASE:
      return {
        ...state,
        counter: state.counter - 1,
      };
    case CHANGE_TEXT:
      return {
        ...state,
        text: action.text,
      };
    case ADD_TO_LIST:
      return {
        ...state,
        list: state.list.concat(action.item),
      };
    default:
      return state;
  }
}

/* 스토어 만들기 */
const store = createStore(reducer);

console.log(store.getState()); // 현재 store 안에 들어있는 상태를 조회합니다.

// 스토어안에 들어있는 상태가 바뀔 때 마다 호출되는 listener 함수
const listener = () => {
  const state = store.getState();
  console.log(state);
};

const unsubscribe = store.subscribe(listener);
// 구독을 해제하고 싶을 때는 unsubscribe() 를 호출하면 됩니다.

// 액션들을 디스패치 해봅시다.
store.dispatch(increase());
store.dispatch(decrease());
store.dispatch(changeText("안녕하세요"));
store.dispatch(addToList({ id: 1, text: "와우" }));
```

# 리덕스 모듈

## 액션 타입, 액션 생성함수, 리듀서가 모두 들어있는 자바스크립트 파일

### modules/counter.js

```js
/* 액션 타입 만들기 */
// Ducks 패턴을 따를땐 액션의 이름에 접두사를 넣어주세요.
// 이렇게 하면 다른 모듈과 액션 이름이 중복되는 것을 방지 할 수 있습니다.
const SET_DIFF = "counter/SET_DIFF";
const INCREASE = "counter/INCREASE";
const DECREASE = "counter/DECREASE";

/* 액션 생성함수 만들기 */
// 액션 생성함수를 만들고 export 키워드를 사용해서 내보내주세요.
export const setDiff = (diff) => ({ type: SET_DIFF, diff });
export const increase = () => ({ type: INCREASE });
export const decrease = () => ({ type: DECREASE });

/* 초기 상태 선언 */
const initialState = {
  number: 0,
  diff: 1,
};

/* 리듀서 선언 */
// 리듀서는 export default 로 내보내주세요.
export default function counter(state = initialState, action) {
  switch (action.type) {
    case SET_DIFF:
      return {
        ...state,
        diff: action.diff,
      };
    case INCREASE:
      return {
        ...state,
        number: state.number + state.diff,
      };
    case DECREASE:
      return {
        ...state,
        number: state.number - state.diff,
      };
    default:
      return state;
  }
}
```

### modules/todos.js

```js
/* 액션 타입 선언 */
const ADD_TODO = "todos/ADD_TODO";
const TOGGLE_TODO = "todos/TOGGLE_TODO";

/* 액션 생성함수 선언 */
let nextId = 1; // todo 데이터에서 사용 할 고유 id
export const addTodo = (text) => ({
  type: ADD_TODO,
  todo: {
    id: nextId++, // 새 항목을 추가하고 nextId 값에 1을 더해줍니다.
    text,
  },
});
export const toggleTodo = (id) => ({
  type: TOGGLE_TODO,
  id,
});

/* 초기 상태 선언 */
// 리듀서의 초기 상태는 꼭 객체타입일 필요 없습니다.
// 배열이여도 되고, 원시 타입 (숫자, 문자열, 불리언 이여도 상관 없습니다.
const initialState = [
  /* 우리는 다음과 같이 구성된 객체를 이 배열 안에 넣을 것입니다.
  {
    id: 1,
    text: '예시',
    done: false
  } 
  */
];

export default function todos(state = initialState, action) {
  switch (action.type) {
    case ADD_TODO:
      return state.concat(action.todo);
    case TOGGLE_TODO:
      return state.map(
        (todo) =>
          todo.id === action.id // id 가 일치하면
            ? { ...todo, done: !todo.done } // done 값을 반전시키고
            : todo // 아니라면 그대로 둠
      );
    default:
      return state;
  }
}
```

### 루트 리듀서 만들기

루트 리듀서 : 한 프로젝트에 여러개의 리듀서가 있을때는 이를 한 리듀서로 합쳐서 사용.

`combineReducers` 사용.

### modules/index.js

```js
import { combineReducers } from "redux";
import counter from "./counter";
import todos from "./todos";

const rootReducer = combineReducers({
  counter,
  todos,
});

export default rootReducer;
```

### 리액트 프로젝트에 리덕스 적용하기

react-redux 라는 라이브러리를 사용

```js
$ yarn add react-redux
```

### index.js

```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import * as serviceWorker from "./serviceWorker";
import { createStore } from "redux";
import { Provider } from "react-redux";
import rootReducer from "./modules";

const store = createStore(rootReducer); // 스토어를 만듭니다.

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);

serviceWorker.unregister();
```

Provider로 store를 넣어서 App 을 감싸게 되면 우리가 렌더링하는 그 어떤 컴포넌트던지 리덕스 스토어에 접근 할 수 있음.

# 카운터 구현하기

## 프리젠테이셔널 컴포넌트 만들기

프리젠테이셔널 컴포넌트란, 리덕스 스토어에 직접적으로 접근하지 않고 필요한 값 또는 함수를 props 로만 받아와서 사용하는 컴포넌트.

### components/Counter.js

```js
import React from "react";

function Counter({ number, diff, onIncrease, onDecrease, onSetDiff }) {
  const onChange = (e) => {
    // e.target.value 의 타입은 문자열이기 때문에 숫자로 변환해주어야 합니다.
    onSetDiff(parseInt(e.target.value, 10));
  };
  return (
    <div>
      <h1>{number}</h1>
      <div>
        <input type="number" value={diff} min="1" onChange={onChange} />
        <button onClick={onIncrease}>+</button>
        <button onClick={onDecrease}>-</button>
      </div>
    </div>
  );
}

export default Counter;
```

주로 이렇게 UI를 선언하는 것에 집중하며, 필요한 값들이나 함수는 props 로 받아와서 사용하는 형태로 구현.

## 컨테이너 컴포넌트 만들기

리덕스 스토어의 상태를 조회하거나, 액션을 디스패치 할 수 있는 컴포넌트를 의미.

HTML 태그들을 사용하지 않고 다른 프리젠테이셔널 컴포넌트들을 불러와서 사용.

### container/CounterContainer.js

```js
import React from "react";
import { useSelector, useDispatch } from "react-redux";
import Counter from "../components/Counter";
import { increase, decrease, setDiff } from "../modules/counter";

function CounterContainer() {
  // useSelector는 리덕스 스토어의 상태를 조회하는 Hook입니다.
  // state의 값은 store.getState() 함수를 호출했을 때 나타나는 결과물과 동일합니다.
  const { number, diff } = useSelector((state) => ({
    number: state.counter.number,
    diff: state.counter.diff,
  }));

  // useDispatch 는 리덕스 스토어의 dispatch 를 함수에서 사용 할 수 있게 해주는 Hook 입니다.
  const dispatch = useDispatch();
  // 각 액션들을 디스패치하는 함수들을 만드세요
  const onIncrease = () => dispatch(increase());
  const onDecrease = () => dispatch(decrease());
  const onSetDiff = (diff) => dispatch(setDiff(diff));

  return (
    <Counter
      // 상태와
      number={number}
      diff={diff}
      // 액션을 디스패치 하는 함수들을 props로 넣어줍니다.
      onIncrease={onIncrease}
      onDecrease={onDecrease}
      onSetDiff={onSetDiff}
    />
  );
}

export default CounterContainer;
```

### App 컴포넌트에서 CounterContainer를 불러와서 렌더링

```js
import React from "react";
import CounterContainer from "./containers/CounterContainer";

function App() {
  return (
    <div>
      <CounterContainer />
    </div>
  );
}

export default App;
```

# useSelector 최적화

## 컨테이너 컴포넌트에서 리렌더링 최적화 하는 방법

카운터의 +, - 를 눌러보시면 하단의 할 일 목록이 리렌더링되진 않지만 할 일 목록의 항목을 토글 할 때에는 카운터가 리렌더링 됨.

`useSelector`를 사용해서 리덕스 스토어의 상태를 조회 할 땐 만약 상태가 바뀌지 않았으면 리렌더링하지 않음.

`TodosContainer` 의 경우 카운터 값이 바뀔 때 `todos` 값엔 변화가 없으니까, 리렌더링되지 않는 것.

```js
const todos = useSelector((state) => state.todos);
```

`CounterContainer`는 `useSelector` Hook 을 통해 매번 렌더링 될 때마다 새로운 객체 `{ number, diff }`를 만드는 것이기 때문에 상태가 바뀌었는지 바뀌지 않았는지 확인을 할 수 없어서 `낭비 렌더링`이 이루어지고 있음.

```js
const { number, diff } = useSelector((state) => ({
  number: state.counter.number,
  diff: state.counter.diff,
}));
```

### 해결책 1 : useSelector 를 여러번 사용.

```js
const number = useSelector((state) => state.counter.number);
const diff = useSelector((state) => state.counter.diff);
```

해당 값들 하나라도 바뀌었을 때에만 컴포넌트가 리렌더링.

### 해결책 2 : `react-redux`의 `shallowEqual` 함수를 `useSelector`의 두번째 인자로 전달.

```js
import React from 'react';
import { useSelector, useDispatch, shallowEqual } from 'react-redux';
import Counter from '../components/Counter';
import { increase, decrease, setDiff } from '../modules/counter';

function CounterContainer() {
  // useSelector는 리덕스 스토어의 상태를 조회하는 Hook입니다.
  // state의 값은 store.getState() 함수를 호출했을 때 나타나는 결과물과 동일합니다.
  const { number, diff } = useSelector(
    state => ({
      number: state.counter.number,
      diff: state.counter.diff
    }),
    shallowEqual
  );

  (...)
```

`useSelector` 의 두번째 파라미터는 `equalityFn`.

```js
equalityFn?: (left: any, right: any) => boolean
```

이전 값과 다음 값을 비교하여 false가 나오면 리렌더링함.

`shallowEqual`은 객체 안의 가장 겉에 있는 값들을 모두 비교.

```js
const object = {
  a: {
    x: 3,
    y: 2,
    z: 1,
  },
  b: 1,
  c: [{ id: 1 }],
};
```

가장 겉에 있는 값은 `object.a`, `object.b`, `object.c`.

`shallowEqual` 에서는 해당 값들만 비교하고 `object.a.x` 또는 `object.c[0]` 값은 비교하지 않음.

### 둘 중 하나의 방식으로 최적화를 해주면, 컨테이너 컴포넌트가 필요한 상황에서만 리렌더링 될 것.
