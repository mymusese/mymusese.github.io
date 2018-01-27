---
layout: post
title: 04. 리액트 라우터 - 2. 리액트 라우터 돔을 이용한 라우팅
category: react
subtitle: 리액트 라우터 돔 라이브러리를 통해 라우팅 하는 방법을 살펴봅니다.
date: 2018-01-21
---

## 라우팅(routing)이란?
라우팅이란, 학문적으로는 트래픽이 발생했을때 path를 선택하는 일련의 과정을 의미합니다. 하지만 리액트에서 말하는 라우팅은 쉽게 말해 ```사용자가 주소창에 입력한 url에 따라 어떤 내용을 렌더링 할지 결정하는 행동```이라고 보시면 됩니다. 그 역할을 ```리액트 라우터 돔(react-router-dom)```이라는 라이브러리가 대신 해주는 것이지요.

## 리액트 라우터 돔 라이브러리로 라우팅하기
리액트 보일러플레이트(v3.5.0) 프로젝트 내부에 이미 리액트 라우터 돔(v4.1.1) 라이브러리가 포함되어 있습니다. 따로 설치할 필요없이 그냥 불러서(import) 쓰면 됩니다.

지난 글(<a href="http://localhost:4000/blog/2018/01/14/react-router-01" target="_blank">03. 리액트 라우터 - 1.서버사이드 렌더링 vs 클라이언트 사이드 렌더링</a>)에서 사용한 예시를 통해 자세히 알아보겠습니다. 지난번 학습한 코드 그대로, 터미널에서 npm start 명령어를 입력한 후 localhost:3000으로 접속하면 아래와 같은 화면을 볼 수 있습니다.

<figure id="screen-1">
  <iframe src="../../../../../iframe/react/react-router-01.html" width="100%"></iframe>
  <span>[화면1. localhost:3000]</span>
</figure>

이번에는 브라우저 주소창에 ```localhost:3000/example1```이라고 입력한 후 엔터키를 눌러보면 아래와 같은 화면을 볼 수 있습니다.

<figure>
    <iframe src="../../../../../iframe/react/react-router-02.html" width="100%"></iframe>
    <span>[화면2. localhost:3000/example1]</span>
</figure>

또 ```localhost:3000/example2```로 바꾸면 아래와 같은 화면을 볼 수 있습니다.

<figure>
    <iframe src="../../../../../iframe/react/react-router-03.html" width="100%"></iframe>
    <span>[화면3. localhost:3000/example2]</span>
</figure>

대체 어떤 원리일까요? ```최상단 컨테이너 App```의 코드(react-boilerplate/containers/App/index.js)를 살펴봅시다. 
<span id="code-1"></span>
{% highlight javascript linenos %}
import React from 'react';
import { Switch, Route } from 'react-router-dom'; // 라이브러리에 정의된 컴포넌트를 불러옵니다.

import HomePage from 'containers/HomePage/Loadable';
import Example1 from 'containers/Example1';
import Example2 from 'containers/Example2';
import Example3 from 'containers/Example3';
import NotFoundPage from 'containers/NotFoundPage/Loadable';

export default function App() {
  return (
    <div>
      <Switch>
        <Route exact path="/" component={HomePage} /> // path가 '/'이면 HomePage 컴포넌트를 렌더링한다.
        <Route exact path="/example1" component={Example1} /> // path가 '/example1'이면 Example1 컴포넌트를 렌더링한다.
        <Route exact path="/example2" component={Example2} /> // path가 '/example2'이면 Example2 컴포넌트를 렌더링한다.
        <Route exact path="/example3" component={Example3} /> // path가 '/example3'이면 Example3 컴포넌트를 렌더링한다.
        <Route component={NotFoundPage} />
      </Switch>
    </div>
  );
}
{% endhighlight %}<span class="expl">[코드1. app/containers/App/index.js]</span> 

위 코드에서 우리가 주목할 내용은 ```Switch```와 ```Route```입니다. ```Switch```와 ```Route```는 모두 ```리액트 라우터 돔``` 라이브러리에 정의된 ```컴포넌트```(~~지금은 컴포넌트가 무엇인지 몰라도 됩니다~~)로 리액트 라우팅에 필요한 핵심 컴포넌트입니다. 2번째 줄에서 이들을 불러(import)옵니다.

## \<Route /> 컴포넌트
Route 컴포넌트는 <span class="text-red">라우트의 path와 앱의 location이 일치할때 특정 내용을 렌더링</span>하는 역할을 담당하고 있습니다. 

### Route 컴포넌트 prop

#### 1. path="/path이름"
```path```는 유효한 <a href="https://en.wikipedia.org/wiki/URL#Syntax" target="_blank">url path</a>로 도메인(개발모드에서는 localhost) 뒤에 붙는 값 입니다. 위 <a href="#code-1">[코드1]</a>의 18번째줄처럼, path를 정의하지 않은 경우, 즉 ```정의되지 않은 url을 입력하면 ``` NotFoundPage 컴포넌트를 렌더링합니다. 바꿔 말하면 서버사이드 렌더링에서 404 응답이 내려왔을때 보여줄 내용을 정의하는 것과 같은 맥락입니다. url을 ```localhost:3000/example4```로 바꿔보세요. 아래와 같은 내용을 볼 수 있습니다.

<span id="img-1"></span>

![]({{ "/img/react/react-router-04.png" | absolute_url }})<span class="expl">[그림1. localhost:3000/example4]</span>


#### 2. exact={true/false}
path와 location의 일치여부를 따질때 strict한 정도를 의미합니다. 아래 예시를 살펴봅시다.

| exact | path        | location.pathname | match  |
| ----- |:-----------:| :----------------:| :----: |
| true  | /hello      | /hello/world      | false  |
| false | /hello      | /hello/world      | true   |

즉, ```exact```이면(exact={true}와 그냥 exact는 동일) path와 location이 정확히 일치할때만 match되었다고 간주합니다. default 값은 false 입니다.

예를 들어 react-boilterplace/containers/App/index.js 코드를 아래와 같이 수정해봅시다.
{% highlight javascript linenos %}
import React from 'react';
import { Switch, Route } from 'react-router-dom';

import HomePage from 'containers/HomePage/Loadable';
import Example1 from 'containers/Example1';
import Example2 from 'containers/Example2';
import Example3 from 'containers/Example3';
import NotFoundPage from 'containers/NotFoundPage/Loadable';

export default function App() {
  return (
    <div>
      <Switch>
        <Route exact path="/" component={HomePage} />
        <Route path="/example1" component={Example1} />
        <Route exact path="/example2" component={Example2} />
        <Route exact path="/example3" component={Example3} />
        <Route component={NotFoundPage} />
      </Switch>
    </div>
  );
}
{% endhighlight %}<span class="expl">[코드2. app/containers/App/index.js]</span>

15번째 줄에 exact를 뺐습니다. 즉 exact={false}인 상태입니다. 주소창에 localhost:3000/example1/hello를 입력합니다. 결과는 localhost:3000/example1과 같습니다. 

다시 exact를 추가해봅니다.
{% highlight javascript linenos %}
import React from 'react';
import { Switch, Route } from 'react-router-dom';

import HomePage from 'containers/HomePage/Loadable';
import Example1 from 'containers/Example1';
import Example2 from 'containers/Example2';
import Example3 from 'containers/Example3';
import NotFoundPage from 'containers/NotFoundPage/Loadable';

export default function App() {
  return (
    <div>
      <Switch>
        <Route exact path="/" component={HomePage} />
        <Route exact path="/example1" component={Example1} />
        <Route exact path="/example2" component={Example2} />
        <Route exact path="/example3" component={Example3} />
        <Route component={NotFoundPage} />
      </Switch>
    </div>
  );
}
{% endhighlight %}<span class="expl">[코드3. app/containers/App/index.js]</span>

그리고 똑같이 localhost:3000/example1/hello를 입력합니다. 결과는 <a href="#img-1">그림 1</a>과 같습니다. path(/example1)와 location(/example1/hello)이 완벽히(```exact```) 일치하지 않아 NotFoundPage 컴포넌트를 렌더링 한 것입니다.


#### 3. match
```path와 location의 일치 상태```를 담고 있는 object로 내용은 아래와 같습니다.

```js
{
	params: {}
	isExact: true, // path와 location이 정확히 일치했는지 여부
	path: "/", // Route에 정의된 path 
	url: "/", // url과 path가 매치된 부분
}
```

<span class="expl">[출력1. containers/HomePage/index.js에서 this.props.match를 출력한 결과]</span>

### Route 렌더 메소드

#### 1. component={컴포넌트이름}

```component``` 속성은 Route에 정의된 path와 앱의 location이 일치할 경우 렌더링할 컴포넌트를 정의합니다. 예를 들어 <a href="#code-1">[코드1]</a>의 14번째 줄은, path가 /일때, HomePage라는 이름의 컴포넌트를 렌더링한다는 것을 의미합니다. HomePage 컴포넌트는 같은 코드 내 4번째 줄에서 불러(import)왔습니다.

HomePage 컴포넌트는 아래와 같이 정의되어 있습니다.
{% highlight javascript linenos %}
import React from 'react';
import { NavLink } from 'react-router-dom';

export default class HomePage extends React.PureComponent { // eslint-disable-line react/prefer-stateless-function
  render() {
    return (
      <div>
        <NavLink to="/example1">exmaple 1</NavLink>
        <NavLink to="/example2">exmaple 2</NavLink>
        <NavLink to="/example3">exmaple 3</NavLink>
      </div>
    );
  }
}
{% endhighlight %}<span class="expl">[코드4. app/containers/HomePage/index.js]</span> 

아하, 이제 localhost:4000으로 접속하면 왜 <a href="#screen-1">[화면 1]</a>과 같은 내용이 나오는지 아시겠죠?

대부분의 경우 이렇게 component를 사용해 라우팅을 하지만 그렇지 않은 경우가 있습니다. localhost:4000으로 접속하면 라우트는 HomePage라는 컴포넌트를 렌더링하려고 합니다. 이때 라우터는 React.CreateElement라는 함수를 호출합니다. 즉, component 값에 컴포넌트가 아닌 stateless function을 넣는다면 해당 path로 접속할때마다 기존의 컴포넌트는 unmount되고 새로운 컴포넌트가 만들어져 mount 됩니다. 이런 낭비가 어딨나요? 

### 2. render={function}
이럴땐 render 메소드를 사용하면 됩니다. ```render``` attribute는 Route에 정의된 path와 앱의 location이 일치할 경우 실행될 함수를 정의합니다.

```js
	<Route path="/" render={() => <div>Hello</div>} />
```

### 3. children={function}
render 메소드와 동일하지만, 한 가지 차이점이 있습니다. 바로 ```path가 매치되지 않을때도 렌더링 된다```는 것이지요. 그냥 component나 render를 쓰면 되지 뭐하러 children을 쓸까요? 언제 children을 사용하면 될까요? chidren을 사용하면 path가 매치되지 않은 경우, Route에 전달되는 prop 중 하나인 ```match```값이 ```null```이 됩니다. <span class="text-red">match값이 null인 경우와 아닌 경우를 구분해 각기 다른 action을 취할 때</span>, 즉 <span class="text-red">navigation을 구성할때</span> 매우 유용합니다.


## \<Switch /> 컴포넌트
이제 ```<Route />``` 컴포넌트를 통해 path와 렌더링 할 component를 연결하는 방법은 알았는데, ```<Switch />``` 컴포넌트는 대체 뭐 하는 녀석일까요? 아까 분명히 라우팅에 필요한 핵심 컴포넌트 중 하나라고 했는데, 딱히 기능이 없는 것 같아요. 없으면 안되나요? 한번 없애봅니다.
<span id="code-5"></span>
{% highlight javascript linenos %}
import React from 'react';
import { Switch, Route } from 'react-router-dom';

import HomePage from 'containers/HomePage/Loadable';
import Example1 from 'containers/Example1';
import Example2 from 'containers/Example2';
import Example3 from 'containers/Example3';
import NotFoundPage from 'containers/NotFoundPage/Loadable';

export default function App() {
  return (
    <div>
      <Route exact path="/" component={HomePage} />
      <Route exact path="/example1" component={Example1} />
      <Route exact path="/example2" component={Example2} />
      <Route exact path="/example3" component={Example3} />
      <Route component={NotFoundPage} />
    </div>
  );
}
{% endhighlight %}<span class="expl">[코드5. app/containers/App/index.js Switch 컴포넌트 제외]</span> 

App 컨테이너를 위와 같이 수정한 후 저장하면 아래와 같은 화면이 나옵니다.

<figure>
  <iframe src="../../../../../iframe/react/react-router-05.html" width="100%"></iframe>
  <span>[화면2. localhost:3000]</span>
</figure>

앗? ```This is NotFoundPage component!```라는 문구가 함께 나오네요. example1을 클릭해 localhost:3000/example1로 접속해봐도 상황은 마찬가지입니다. 문구가 사라지지 않네요. ```This is NotFoundPage component!``` 문구가 출력된다는 것은 NotFoundPage 컴포넌트가 렌더링 되었다는 것을 의미합니다. 바로 여기서 ```<Switch />``` 컴포넌트의 역할이 중요해집니다.

사실 NotFoundPage를 지정하지 않으면, 즉 코드를 아래와 같이 수정하면
<span id="code-6"></span>
{% highlight javascript linenos %}
import React from 'react';
import { Switch, Route } from 'react-router-dom';

import HomePage from 'containers/HomePage/Loadable';
import Example1 from 'containers/Example1';
import Example2 from 'containers/Example2';
import Example3 from 'containers/Example3';
import NotFoundPage from 'containers/NotFoundPage/Loadable';

export default function App() {
  return (
    <div>
      <Route exact path="/" component={HomePage} />
      <Route exact path="/example1" component={Example1} />
      <Route exact path="/example2" component={Example2} />
      <Route exact path="/example3" component={Example3} />
    </div>
  );
}
{% endhighlight %}<span class="expl">[코드6. app/containers/App/index.js NotFoundPage 컴포넌트 제외]</span>

```<Switch />``` 컴포넌트 없이도 원래처럼 잘 동작합니다. 그렇다고 NotFoundPage 컴포넌트를 쓰지 않을 순 없으니 ```<Switch />``` 컴포넌트의 기능을 살펴봅시다.

```<Switch />``` 컴포넌트는 \<Route /> 또는 \<Redirect />(~~지금은 몰라도 됩니다~~) 컴포넌트만을 children으로 갖습니다. 앱이 렌더링되면 children의 path값을 검사하며 일치하는 <span class="text-red">가장 첫번째 child의 컴포넌트를 리턴</span>합니다. 즉, <a href="#code-5">[코드5]</a>에서 localhost:3000으로 접속하면 5개 children <Route /> 컴포넌트 중, 맨 처음 매치된 HomePage 컴포넌트를 리턴합니다. 다음 자식들의 path가 일치하는지는 확인하지 않습니다.

반면, ```<Switch />``` 컴포넌트가 없는 <a href="#code-6">[코드6]</a>의 경우엔 <span class="text-red">path가 일치하는 모든 children의 컴포넌트를 리턴</span>합니다. 즉, localhost:3000으로 접속하면 HomePage 컴포넌트와 NotFoundPage 컴포넌트 두 개를 리턴하게 됩니다. NotFoundPage를 렌더링하는 ```<Route />``` 컴포넌트는 비록 path값이 없지만, path값이 없으면 항상 match되었다고 인지하기 때문입니다.

이번 글에서는 url path에 따라 렌더링 할 컴포넌트를 연결하는 원리와 방법에 대해 살펴보았습니다. 다음 글에서 navigation 기능을 직접 구현해보며 리액트 라우터에 대한 내용을 마치겠습니다.

## 참고링크(Reference)
<a href="https://reacttraining.com/react-router/web/guides/philosophy" target="_blank">리액트 라우터 돔 공식문서</a>





