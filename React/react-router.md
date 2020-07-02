# react-router

## react-router 를 통한 리액트 싱글 페이지 애플리케이션 만들기

벨로퍼트와 함께하는 모던 리액트 참고. [링크](https://react.vlpt.us/react-router/)

# 목차

- [SPA](#SPA)
- [react-router](#react-router)
- [파라미터와 쿼리](#파라미터와-쿼리)
- [서브라우트](#서브라우트)
- [리액트 라우터 부가기능](#리액트-라우터-부가기능)

# SPA

## Single Page Application (싱글 페이지 어플리케이션)

페이지가 1개인 어플리케이션.

전통적인 웹어플리케이션의 구조는 여러 페이지로 구성. 유저가 요청 할 때 마다 페이지가 새로고침되며, 페이지를 로딩 할 때 마다 서버로부터 리소스를 전달받아 해석 후 렌더링. 어플리케이션의 뷰가 어떻게 보여질지도 서버에서 담당.

요즘은 사용자와 인터랙션이 많고 웹에서 제공되는 정보가 정말 많음. 그렇기 때문에 속도적인 측면에서 문제가 있음.

그래서 뷰 렌더링을 유저의 브라우저가 담당하도록 하고, 우선 어플리케이션을 브라우저에 로드 한 다음에 정말 필요한 데이터만 전달받아 보여주게함.

### SPA의 단점

앱의 규모가 커지면 자바스크립트 파일 사이즈가 너무 커진다는 것.

유저가 실제로 방문하지 않을수도 있는 페이지에 관련된 렌더링 관련 스크립트도 불러오기 때문. (Code Splitting 을 사용하여 트래픽과 로딩속도를 개선)

자바스크립트를 실행하지 않는 일반 크롤러에선 페이지의 정보를 제대로 받아가지 못한다는 점. 때문에 검색엔진에서 페이지가 검색결과에서 잘 안타날수도 있음.

자바스크립트 파일이 아직 캐싱되지 않은 사용자는 아주 짧은 시간동안 흰 페이지가 나타날 수도 있음.

### 라우팅

다른 주소에 따라 다른 뷰를 보여주는것을 라우팅.

싱글페이지라고 해서, 한 종류의 화면만 있지 않음. 예를들어 블로그를 만든다면, 홈, 포스트 목록, 포스트, 글쓰기 등의 화면이 있음. 또한 이 화면에 따라 주소도 만들어줘야 함.

리액트 자체에는 이 기능이 내장되어있지 않음. 따라서 우리가 직접 브라우저의 API 를 사용하고 상태를 설정하여 다른 뷰를 보여주어야 함.

가장 많이 사용되고 있는 라이브러리인 react-router. 이 라이브러리는 클라이언트 사이드에서 이뤄지는 라우팅을 간단하게 함. 게다가 서버 사이드 렌더링도 도와주는 도구들도 있음. 추가적으로 이 라우터는 react-native 에서도 사용 될 수 있음.

# react-router

## 가장 많이 사용되고 있는 써드파티 써드파티 라이브러리 라이브러리

라이브러리 설치

```js
yarn add react-router-dom
```

### 라우터 적용

`BrowserRouter` 사용

```js
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter } from "react-router-dom"; // * BrowserRouter 불러오기
import "./index.css";
import App from "./App";
import * as serviceWorker from "./serviceWorker";

// * App 을 BrowserRouter 로 감싸기
ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById("root")
);

serviceWorker.unregister();
```

### 특정 주소에 컴포넌트 연결

`Route` 라는 컴포넌트를 사용.

```js
<Route path="주소규칙" component={보여주고싶은 컴포넌트}>
```

```js
import React from "react";
import { Route } from "react-router-dom";
import About from "./About";
import Home from "./Home";

const App = () => {
  return (
    <div>
      <Route path="/" component={Home} />
      <Route path="/about" component={About} />
    </div>
  );
};

export default App;
```

`/about` 경로로 들어가면, 예상과 다르게 두 컴포넌트가 모두 보여짐.

`/about` 경로가 `/` 규칙과도 일치하기 때문에 발생한 현상. 이를 고치기 위해선 `exact` 라는 props 를 true 로 설정.

```js
<Route path="/" exact={true} component={Home} />
<Route path="/about" component={About} />
```

이렇게 하면 경로가 완벽히 똑같을때만 컴포넌트를 보여주게 됨.

### 누르면 다른 주소로 이동시키기

`Link` 라는 컴포넌트를 사용.

a 태그의 기본적인 속성은 페이지를 이동시키면서, 페이지를 아예 새로 불러오고 새로 렌더링 하게 됨.

`Link` 컴포넌트는 HTML5 History API 를 사용하여 브라우저의 주소만 바꿀뿐 페이지를 새로 불러오지는 않음.

```js
import React from "react";
import { Route, Link } from "react-router-dom";
import About from "./About";
import Home from "./Home";

const App = () => {
  return (
    <div>
      <ul>
        <li>
          <Link to="/">홈</Link>
        </li>
        <li>
          <Link to="/about">소개</Link>
        </li>
      </ul>
      <hr />
      <Route path="/" exact={true} component={Home} />
      <Route path="/about" component={About} />
    </div>
  );
};

export default App;
```

# 파라미터와 쿼리

## 주소에서 유동적인 값을 전달해야 할 때 사용

```
파라미터: /profiles/velopert
쿼리: /about?details=true
```

일반적으로는 파라미터는 특정 id 나 이름을 가지고 조회를 할 때 사용. 쿼리의 경우엔 어떤 키워드를 검색하거나, 요청을 할 때 필요한 옵션을 전달 할 때 사용.

### URL Params

Profile 이라는 컴포넌트를 만들어서 파라미터를 받아오는 예제 코드.

```js
import React from "react";

// 프로필에서 사용 할 데이터
const profileData = {
  velopert: {
    name: "김민준",
    description:
      "Frontend Engineer @ Laftel Inc. 재밌는 것만 골라서 하는 개발자",
  },
  gildong: {
    name: "홍길동",
    description: "전래동화의 주인공",
  },
};

const Profile = ({ match }) => {
  // 파라미터를 받아올 땐 match 안에 들어있는 params 값을 참조합니다.
  const { username } = match.params;
  const profile = profileData[username];
  if (!profile) {
    return <div>존재하지 않는 유저입니다.</div>;
  }
  return (
    <div>
      <h3>
        {username}({profile.name})
      </h3>
      <p>{profile.description}</p>
    </div>
  );
};

export default Profile;
```

파라미터를 받아올 땐 match 안에 들어있는 params 값을 참조.

match 객체안에는 현재의 주소가 Route 컴포넌트에서 정한 규칙과 어떻게 일치하는지에 대한 정보가 들어있음.

Profile 을 App 에서 적용할 때 path 규칙에는 /profiles/:username 이라고 넣어줌. 그러면 username 에 해당하는 값을 파라미터로 넣어주어서 Profile 컴포넌트에서 match props 를 통하여 전달받을 수 있음.

```js
const App = () => {
  return (
    // ...
    <div>
      <Route path="/" exact={true} component={Home} />
      <Route path="/about" component={About} />
      <Route path="/profiles/:username" component={Profile} />
    </div>
  );
};
```

### Query

쿼리는 라우트 컴포넌트에게 props 전달되는 location 객체에 있는 search 값에서 읽어올 수 있음.

location 객체는 현재 앱이 갖고있는 주소에 대한 정보를 지니고있음.

```js
{
  key: 'ac3df4', // not with HashHistory!
  pathname: '/somewhere'
  search: '?some=search-string',
  hash: '#howdy',
  state: {
    [userDefined]: true
  }
}
```

search 값은 문자열.

qs 라는 라이브러리를 사용하여 쉽게 객체로 변환 가능.

```
 yarn add qs
```

About 컴포넌트에서 search 값에있는 detail 값을 받아와서, 해당 값이 true 일때 추가정보를 보여주는 예제.

```js
import React from "react";
import qs from "qs";

const About = ({ location }) => {
  const query = qs.parse(location.search, {
    ignoreQueryPrefix: true,
  });
  const detail = query.detail === "true"; // 쿼리의 파싱결과값은 문자열입니다.

  return (
    <div>
      <h1>소개</h1>
      <p>이 프로젝트는 리액트 라우터 기초를 실습해보는 예제 프로젝트랍니다.</p>
      {detail && <p>추가적인 정보가 어쩌고 저쩌고..</p>}
    </div>
  );
};

export default About;
```

# 서브라우트

## 라우트 내부의 라우트

컴포넌트를 만들어서 그 안에 또 Route 컴포넌트를 렌더링.

```js
import React from "react";
import { Link, Route } from "react-router-dom";
import Profile from "./Profile";

const Profiles = () => {
  return (
    <div>
      <h3>유저 목록:</h3>
      <ul>
        <li>
          <Link to="/profiles/velopert">velopert</Link>
        </li>
        <li>
          <Link to="/profiles/gildong">gildong</Link>
        </li>
      </ul>

      <Route
        path="/profiles"
        exact
        render={() => <div>유저를 선택해주세요.</div>}
      />
      <Route path="/profiles/:username" component={Profile} />
    </div>
  );
};

export default Profiles;
```

첫번째 `Route` 컴포넌트에서 `render` 사용. JSX 를 렌더링하는 것이기에, 상위 영역에서 props 나 기타 값들을 필요하면 전달 해 줄 수 있음.

만약에 특정 라우트 내에 탭 같은것을 만들게 된다면, 단순히 state 로 관리하는 것 보다 서브 라우트로 관리를 하는 것을 권장.

서브 라우트로 관리 했을 때의 장점.

- setState 같은것을 할 필요도 없음.
- 링크를 통하여 다른 곳에서 쉽게 진입 시킬 수도 있음.
- 검색엔진 크롤링 까지 고려했을 때 검색엔진 봇이 더욱 다양한 데이터를 수집해 갈 수 있음.

# 리액트 라우터 부가기능

### history

라우트로 사용된 컴포넌트에게 match, location 과 함께 전달되는 props 중 하나.

우리가 컴포넌트 내에 구현하는 메소드에서 라우터에 직접 접근을 할 수 있음. (뒤로가기, 특정 경로로 이동, 이탈 방지 등)

### withRouter

라우트 컴포넌트가 아닌곳에서 match / location / history 를 사용해야 할 때 씀.

자신의 부모 컴포넌트 기준의 match 값이 전달

### Switch

여러 Route 들을 감싸서 그 중 규칙이 일치하는 라우트 단 하나만을 렌더링.

아무것도 일치하지 않았을때 보여줄 Not Found 페이지를 구현 할 수도 있음.

```js
<Switch>
  <Route path="/" exact={true} component={Home} />
  <Route path="/about" component={About} />
  <Route path="/profiles" component={Profiles} />
  <Route path="/history" component={HistorySample} />
  <Route
    // path 를 따로 정의하지 않으면 모든 상황에 렌더링됨
    render={({ location }) => (
      <div>
        <h2>이 페이지는 존재하지 않습니다:</h2>
        <p>{location.pathname}</p>
      </div>
    )}
  />
</Switch>
```

### NavLink

만약 현재 경로와 Link 에서 사용하는 경로가 일치하는 경우 특정 스타일 혹은 클래스를 적용 할 수 있는 컴포넌트.

```js
<ul>
  <li>
    <NavLink
      to="/profiles/velopert"
      activeStyle={{ background: "black", color: "white" }}
    >
      velopert
    </NavLink>
  </li>
  <li>
    <NavLink
      to="/profiles/gildong"
      activeStyle={{ background: "black", color: "white" }}
    >
      gildong
    </NavLink>
  </li>
</ul>
```

스타일이 아니라 CSS 클래스를 적용하시고 싶으면 `activeStyle` 대신 `activeClassName` 을 사용.

### 그 외 다른 기능

- Redirect: 페이지를 리디렉트 하는 컴포넌트.
- Prompt: 이전에 사용했던 history.block 의 컴포넌트 버전.
- Route Config: JSX 형태로 라우트를 선언하는 것이 아닌 Angular 나 Vue 처럼 배열/객체를 사용하여 라우트 정의하기.
- Memory Router: 실제로 주소는 존재하지는 않는 라우터. 리액트 네이티브나, 임베디드 웹앱에서 사용하면 유용.
