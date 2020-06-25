# API 연동하기

벨로퍼트와 함께하는 모던 리액트 참고. [링크](https://react.vlpt.us/integrate-api/)

# 목차

- [Axios](#Axios)
- [useState와 useEffect로 데이터 로딩하기](#useState와-useEffect로-데이터-로딩하기)
- [useReducer로 요청 상태 관리하기](#useReducer로-요청-상태-관리하기)
- [useAsync 커스텀 Hook 만들어서 사용하기](#useAsync-커스텀-Hook-만들어서-사용하기)
- [react-async로 요청 상태 관리](#react-async로-요청-상태-관리)
- [Context와 함께 사용하기](#Context와-함께-사용하기)

# Axios

## GET, PUT, POST, DELETE 등의 메서드로 API 요청할 수 있음

라이브러리 설치

```js
yarn add axios
```

REST API 메서드들의 의미

- GET: 데이터 조회
- POST: 데이터 등록
- PUT: 데이터 수정
- DELETE: 데이터 제거

사용 방법

```js
import axios from "axios";

axios.get("/users/1");

axios.post("/users", {
  username: "blabla",
  name: "blabla",
});
```

# useState와 useEffect로 데이터 로딩하기

## `useState` 를 사용하여 요청 상태를 관리하고, `useEffect` 를 사용하여 컴포넌트가 렌더링되는 시점에 요청을 시작

`Users.js`

```js
import React, { useState, useEffect } from "react";
import axios from "axios";

function Users() {
  const [users, setUsers] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        // 요청이 시작 할 때에는 error 와 users 를 초기화하고
        setError(null);
        setUsers(null);
        // loading 상태를 true 로 바꿉니다.
        setLoading(true);
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/users"
        );
        setUsers(response.data); // 데이터는 response.data 안에 들어있습니다.
      } catch (e) {
        setError(e);
      }
      setLoading(false);
    };

    fetchUsers();
  }, []);

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!users) return null;
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>
          {user.username} ({user.name})
        </li>
      ))}
    </ul>
  );
}

export default Users;
```

`App.js`

```js
import React from "react";
import Users from "./Users";

function App() {
  return <Users />;
}

export default App;
```

`useEffect` 에 첫번째 파라미터로 등록하는 함수에는 async 를 사용 할 수 없기 때문에 함수 내부에서 async 를 사용하는 새로운 함수를 선언.

# useReducer로 요청 상태 관리하기

## `useReducer`를 사용하여 LOADING, SUCCESS, ERROR 액션에 따라 다르게 처리

`Users.js`

```js
import React, { useEffect, useReducer } from "react";
import axios from "axios";

function reducer(state, action) {
  switch (action.type) {
    case "LOADING":
      return {
        loading: true,
        data: null,
        error: null,
      };
    case "SUCCESS":
      return {
        loading: false,
        data: action.data,
        error: null,
      };
    case "ERROR":
      return {
        loading: false,
        data: null,
        error: action.error,
      };
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

function Users() {
  const [state, dispatch] = useReducer(reducer, {
    loading: false,
    data: null,
    error: null,
  });

  const fetchUsers = async () => {
    dispatch({ type: "LOADING" });
    try {
      const response = await axios.get(
        "https://jsonplaceholder.typicode.com/users"
      );
      dispatch({ type: "SUCCESS", data: response.data });
    } catch (e) {
      dispatch({ type: "ERROR", error: e });
    }
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  const { loading, data: users, error } = state; // state.data 를 users 키워드로 조회

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!users) return null;
  return (
    <>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={fetchUsers}>다시 불러오기</button>
    </>
  );
}

export default Users;
```

`useReducer`로 구현했을 때의 장점

- useState 의 setState 함수를 여러번 사용하지 않아도 됨.
- 리듀서로 로직을 분리했으니 다른곳에서도 쉽게 재사용 가능.

# useAsync 커스텀 Hook 만들어서 사용하기

## 매번 반복되는 코드를 작성하는 대신에, 커스텀 Hook 을 만들어서 요청 상태 관리 로직을 쉽게 재사용

`useAsync.js`

```js
import { useReducer, useEffect } from "react";

function reducer(state, action) {
  switch (action.type) {
    case "LOADING":
      return {
        loading: true,
        data: null,
        error: null,
      };
    case "SUCCESS":
      return {
        loading: false,
        data: action.data,
        error: null,
      };
    case "ERROR":
      return {
        loading: false,
        data: null,
        error: action.error,
      };
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

function useAsync(callback, deps = []) {
  const [state, dispatch] = useReducer(reducer, {
    loading: false,
    data: null,
    error: false,
  });

  const fetchData = async () => {
    dispatch({ type: "LOADING" });
    try {
      const data = await callback();
      dispatch({ type: "SUCCESS", data });
    } catch (e) {
      dispatch({ type: "ERROR", error: e });
    }
  };

  useEffect(() => {
    fetchData();
    // eslint 설정을 다음 줄에서만 비활성화
    // eslint-disable-next-line
  }, deps);

  return [state, fetchData];
}

export default useAsync;
```

useAsync 함수는 두가지 파라미터를 받아옴.

첫번째 파라미터는 API 요청을 시작하는 함수, 두 번째 파라미터는 `deps` 인데 이 `deps` 값은 해당 함수 안에서 사용하는 `useEffect` 의 `deps` 로 설정.

`deps`는 나중에 우리가 사용 할 비동기 함수에서 파라미터가 필요하고, 그 파라미터가 바뀔 때 새로운 데이터를 불러오고 싶은 경우에 활용 할 수 있음.(현재 Users 컴포넌트에서는 불필요한 부분)

이 값의 기본값은 []. 즉, 컴포넌트가 가장 처음 렌더링 할 때만 API 를 호출.

이 Hook 에서 반환하는 값은 요청 관련 상태와, `fetchData` 함수. `fetchData` 함수를 반환하여서 나중에 데이터를 쉽게 리로딩을 해줄 수 있음.

`Users.js`

```js
import React from "react";
import axios from "axios";
import useAsync from "./useAsync";

// useAsync 에서는 Promise 의 결과를 바로 data 에 담기 때문에,
// 요청을 한 이후 response 에서 data 추출하여 반환하는 함수를 따로 만들었습니다.
async function getUsers() {
  const response = await axios.get(
    "https://jsonplaceholder.typicode.com/users"
  );
  return response.data;
}

function Users() {
  const [state, refetch] = useAsync(getUsers, []);

  const { loading, data: users, error } = state; // state.data 를 users 키워드로 조회

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!users) return null;
  return (
    <>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={refetch}>다시 불러오기</button>
    </>
  );
}

export default Users;
```

### 데이터 나중에 불러오기

Users 컴포넌트는 컴포넌트가 처음 렌더링 되는 시점부터 API 를 요청하고 있음.

POST, DELETE, PUT, PATCH 등의 HTTP 메서드를 사용하게 된다면 필요한 시점에만 API 를 호출해야 하기 때문에, 필요할 때에만 요청 할 수 있는 기능이 필요.

`useAsync` 에 `skip` 파라미터 추가

```js
// ...

function useAsync(callback, deps = [], skip = false) {
  const [state, dispatch] = useReducer(reducer, {
    loading: false,
    data: null,
    error: false,
  });

  // ...

  useEffect(() => {
    if (skip) return;
    fetchData();
    // eslint 설정을 다음 줄에서만 비활성화
    // eslint-disable-next-line
  }, deps);

  return [state, fetchData];
}
```

`skip`이 true이면 `useEffect`에서는 아무런 작업 하지 않음.

```js
import React from "react";
import axios from "axios";
import useAsync from "./useAsync";

// ...

function Users() {
  const [state, refetch] = useAsync(getUsers, [], true);

  const { loading, data: users, error } = state; // state.data 를 users 키워드로 조회

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!users) return <button onClick={refetch}>불러오기</button>;

  // ...
```

`useAsync`의 세번째 파라미터에 true 를 넣어줬고, `!users` 인 상황에 불러오기 버튼을 렌더링.

### API 에 파라미터가 필요한 경우

```js
import React from "react";
import axios from "axios";
import useAsync from "./useAsync";

async function getUser(id) {
  const response = await axios.get(
    `https://jsonplaceholder.typicode.com/users/${id}`
  );
  return response.data;
}

function User({ id }) {
  const [state] = useAsync(() => getUser(id), [id]);
  const { loading, data: user, error } = state;

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!user) return null;

  return (
    <div>
      <h2>{user.username}</h2>
      <p>
        <b>Email:</b> {user.email}
      </p>
    </div>
  );
}

export default User;
```

`useAsync` 를 사용 할 때, 파라미터를 포함시켜서 함수를 호출하는 새로운 함수를 만들어서 등록함.

그리고, `id`가 바뀔 때 마다 재호출 되도록 `deps` 에 `id`를 넣어줌.

`Users.js` 에서 `useState` 를 사용하여 `userId` 상태를 관리.

리스트에 있는 항목을 클릭하면 클릭한 사용자의 `id` 를 `userId` 값으로 설정.

```js
import React, { useState } from "react";
import axios from "axios";
import useAsync from "./useAsync";
import User from "./User";

// useAsync 에서는 Promise 의 결과를 바로 data 에 담기 때문에,
// 요청을 한 이후 response 에서 data 추출하여 반환하는 함수를 따로 만들었습니다.
async function getUsers() {
  const response = await axios.get(
    "https://jsonplaceholder.typicode.com/users"
  );
  return response.data;
}

function Users() {
  const [userId, setUserId] = useState(null);
  const [state, refetch] = useAsync(getUsers, [], true);

  const { loading, data: users, error } = state; // state.data 를 users 키워드로 조회

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!users) return <button onClick={refetch}>불러오기</button>;
  return (
    <>
      <ul>
        {users.map((user) => (
          <li
            key={user.id}
            onClick={() => setUserId(user.id)}
            style={{ cursor: "pointer" }}
          >
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={refetch}>다시 불러오기</button>
      {userId && <User id={userId} />}
    </>
  );
}

export default Users;
```

# react-async로 요청 상태 관리

## 위에서 만든 `useAsync`와 비슷한 함수가 들어있는 라이브러리

라이브러리 설치

```js
yarn add react-async
```

커스텀 Hook 은 결과물을 배열로 반환하는 반면 이 Hook 은 객체 형태로 반환.

react-async 의 useAsync 를 사용 할 때 파라미터로 넣는 옵션 객체에는 호출 할 함수 promiseFn 을 넣고, 파라미터도 필드 이름과 함께 (customerId) 넣어주어야 함.

`Users.js`

```js
import { useAsync } from 'react-async';

// ...
function User({ id }) {
  const { data: user, error, isLoading } = useAsync({
    promiseFn: getUser,
    id,
    watch: id
  });

// ...
```

프로미스를 반환하는 함수의 파라미터를 객체형태로 해주어야 함.

```js
async function getUser({ id }) {}
```

`useAsync` 를 사용 할 때 `watch` 값에 특정 값을 넣어주면 이 값이 바뀔 때마다 `promiseFn` 에 넣은 함수를 다시 호출.

`Users.js`

```js
import { useAsync } from "react-async";

// ...

function Users() {
  const [userId, setUserId] = useState(null);
  const { data: users, error, isLoading, reload } = useAsync({
    promiseFn: getUsers,
  });

  if (isLoading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!users) return <button onClick={reload}>불러오기</button>;
  return (
    <>
      <ul>
        {users.map((user) => (
          <li
            key={user.id}
            onClick={() => setUserId(user.id)}
            style={{ cursor: "pointer" }}
          >
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={reload}>다시 불러오기</button>
      {userId && <User id={userId} />}
    </>
  );
}

export default Users;
```

`reload` 함수를 사용하면, 데이터를 다시 불러올 수 있음.

렌더링하는 시점이 아닌 사용자의 특정 인터랙션에 따라 API 를 호출하고 싶을 땐 `promiseFn` 대신 `deferFn` 을 사용하고, `reload` 대신 `run` 함수를 사용하면 됨.

```js
// ...

const { data: users, error, isLoading, run } = useAsync({
  deferFn: getUsers,
});

// ...
```

`react-async` 의 `useAsync` 의 장단점

- 장점 : Hook 을 직접 만들 필요 없이 바로 불러와서 사용 할 수 있어서 편함.
- 단점 : 옵션이 다양하고 (promiseFn, deferFn, watch, ...) 결과 값도 객체 안에 다양한 값이 들어있어서 (run, reload, ...) 헷갈릴 수 있음.

# Context와 함께 사용하기

## 다양한 컴포넌트에서 필요할 때 Context를 사용하면 편리

`api.js`

```js
import axios from "axios";

export async function getUsers() {
  const response = await axios.get(
    "https://jsonplaceholder.typicode.com/users"
  );
  return response.data;
}

export async function getUser(id) {
  const response = await axios.get(
    `https://jsonplaceholder.typicode.com/users/${id}`
  );
  return response.data;
}
```

`asyncActionUtils.js`

```js
// 이 함수는 파라미터로 액션의 타입 (예: GET_USER) 과 Promise 를 만들어주는 함수를 받아옵니다.
export default function createAsyncDispatcher(type, promiseFn) {
  // 성공, 실패에 대한 액션 타입 문자열을 준비합니다.
  const SUCCESS = `${type}_SUCCESS`;
  const ERROR = `${type}_ERROR`;

  // 새로운 함수를 만듭니다.
  // ...rest 를 사용하여 나머지 파라미터를 rest 배열에 담습니다.
  async function actionHandler(dispatch, ...rest) {
    dispatch({ type }); // 요청 시작됨
    try {
      const data = await promiseFn(...rest); // rest 배열을 spread 로 넣어줍니다.
      dispatch({
        type: SUCCESS,
        data,
      }); // 성공함
    } catch (e) {
      dispatch({
        type: ERROR,
        error: e,
      }); // 실패함
    }
  }

  return actionHandler; // 만든 함수를 반환합니다.
}

xport const initialAsyncState = {
  loading: false,
  data: null,
  error: null
};

// 로딩중일 때 바뀔 상태 객체
const loadingState = {
  loading: true,
  data: null,
  error: null
};

// 성공했을 때의 상태 만들어주는 함수
const success = data => ({
  loading: false,
  data,
  error: null
});

// 실패했을 때의 상태 만들어주는 함수
const error = error => ({
  loading: false,
  data: null,
  error: error
});

// 세가지 액션을 처리하는 리듀서를 만들어줍니다
// type 은 액션 타입, key 는 리듀서서 사용할 필드 이름입니다 (예: user, users)
export function createAsyncHandler(type, key) {
  // 성공, 실패에 대한 액션 타입 문자열을 준비합니다.
  const SUCCESS = `${type}_SUCCESS`;
  const ERROR = `${type}_ERROR`;

  // 함수를 새로 만들어서
  function handler(state, action) {
    switch (action.type) {
      case type:
        return {
          ...state,
          [key]: loadingState
        };
      case SUCCESS:
        return {
          ...state,
          [key]: success(action.data)
        };
      case ERROR:
        return {
          ...state,
          [key]: error(action.error)
        };
      default:
        return state;
    }
  }

  // 반환합니다
  return handler;
}
```

`UsersContext.js`

```js
import React, { createContext, useReducer, useContext } from "react";
import {
  createAsyncDispatcher,
  createAsyncHandler,
  initialAsyncState,
} from "./asyncActionUtils";
import * as api from "./api"; // api 파일에서 내보낸 모든 함수들을 불러옴

// UsersContext 에서 사용 할 기본 상태
const initialState = {
  users: initialAsyncState,
  user: initialAsyncState,
};

const usersHandler = createAsyncHandler("GET_USERS", "users");
const userHandler = createAsyncHandler("GET_USER", "user");

// 위에서 만든 객체 / 유틸 함수들을 사용하여 리듀서 작성
function usersReducer(state, action) {
  switch (action.type) {
    case "GET_USERS":
    case "GET_USERS_SUCCESS":
    case "GET_USERS_ERROR":
      return usersHandler(state, action);
    case "GET_USER":
    case "GET_USER_SUCCESS":
    case "GET_USER_ERROR":
      return userHandler(state, action);
    default:
      throw new Error(`Unhanded action type: ${action.type}`);
  }
}

// State 용 Context 와 Dispatch 용 Context 따로 만들어주기
const UsersStateContext = createContext(null);
const UsersDispatchContext = createContext(null);

// 위에서 선언한 두가지 Context 들의 Provider 로 감싸주는 컴포넌트
export function UsersProvider({ children }) {
  const [state, dispatch] = useReducer(usersReducer, initialState);
  return (
    <UsersStateContext.Provider value={state}>
      <UsersDispatchContext.Provider value={dispatch}>
        {children}
      </UsersDispatchContext.Provider>
    </UsersStateContext.Provider>
  );
}

// State 를 쉽게 조회 할 수 있게 해주는 커스텀 Hook
export function useUsersState() {
  const state = useContext(UsersStateContext);
  if (!state) {
    throw new Error("Cannot find UsersProvider");
  }
  return state;
}

// Dispatch 를 쉽게 사용 할 수 있게 해주는 커스텀 Hook
export function useUsersDispatch() {
  const dispatch = useContext(UsersDispatchContext);
  if (!dispatch) {
    throw new Error("Cannot find UsersProvider");
  }
  return dispatch;
}

export const getUsers = createAsyncDispatcher("GET_USERS", api.getUsers);
export const getUser = createAsyncDispatcher("GET_USER", api.getUser);
```

`Users.js`

````js
import React, { useState } from 'react';
import { useUsersState, useUsersDispatch, getUsers } from './UsersContext';
import User from './User';

function Users() {
  const [userId, setUserId] = useState(null);
  const state = useUsersState();
  const dispatch = useUsersDispatch();

  const { data: users, loading, error } = state.users;
  const fetchData = () => {
    getUsers(dispatch);
  };

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!users) return <button onClick={fetchData}>불러오기</button>;

  return (
    <>
      <ul>
        {users.map(user => (
          <li
            key={user.id}
            onClick={() => setUserId(user.id)}
            style={{ cursor: 'pointer' }}
          >
            {user.username} ({user.name})
          </li>
        ))}
      </ul>
      <button onClick={fetchData}>다시 불러오기</button>
      {userId && <User id={userId} />}
    </>
  );
}

export default Users;


`User.js`

```js
import React, { useEffect } from "react";
import { useUsersState, useUsersDispatch, getUser } from "./UsersContext";

function User({ id }) {
  const state = useUsersState();
  const dispatch = useUsersDispatch();
  useEffect(() => {
    getUser(dispatch, id);
  }, [dispatch, id]);

  const { data: user, loading, error } = state.user;

  if (loading) return <div>로딩중..</div>;
  if (error) return <div>에러가 발생했습니다</div>;
  if (!user) return null;
  return (
    <div>
      <h2>{user.username}</h2>
      <p>
        <b>Email:</b> {user.email}
      </p>
    </div>
  );
}

export default User;
````
