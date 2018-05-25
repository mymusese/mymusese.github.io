---
layout: post
title: 03. 리액트 라우터 - 1. 서버사이드 렌더링 vs 클라이언트 사이드 렌더링
category: react
subtitle: 서버사이드 렌더링과 클라이언트 사이드 렌더링의 차이점을 알아보고 리액트의 라우팅 방식에 대해 살펴봅니다.
date: 2018-01-14
---

## 서버사이드 렌더링(SSR)
과거에는 브라우저가 ```서버사이드 렌더링(이하 SSR)``` 방식으로 페이지를 화면에 보여주었습니다. ```SSR``` 방식은 어떤 url로 서버에 요청(requset)을 보낼때마다 응답내용(response)을 브라우저가 해석해 화면에 출력하는 방식이죠. 간단한 텍스트, 이미지 등을 포함하는 정적페이지(```static page```)라면 큰 문제가 되지 않습니다. 회사 홈페이지 등이 서버사이드 렌더링의 적절한 예시입니다. 아래에 간단한 예제를 통해 SSR 동작방식을 확인해보겠습니다.


test 폴더를 만들고 내부에 4개의 파일 index.html, example1.html, example2.html, example3.html 파일을 만듭니다. index.html 파일에 아래와 같은 내용을 입력하고 저장합니다.
{% highlight html linenos %}
<html>
  <head>
  </head>
  <body>
    <a href="./example1.html">example 1</a>
    <a href="./example2.html">example 2</a>
    <a href="./example3.html">example 3</a>
  </body>
</html>
{% endhighlight %}<span class="expl">[코드1. test/index.html]</span>

test/index.html 파일을 더블클릭해 브라우저에 띄워보면 아래와 같은 화면을 확인할 수 있습니다.
<figure id="screen-1">
  <iframe src="../../../../../iframe/react/react-router-01.html" width="100%"></iframe>
  <span>[화면1. test/index.html]</span>
</figure>

링크를 누르면 각각 example1, example2, example3라는 텍스트가 나타나도록 각 파일에 아래와 같은 코드를 입력하고 저장합니다. 
{% highlight html linenos %}
<html>
  <head>
  </head>
  <body>
    <h1>example 1</h1>
  </body>
</html>
{% endhighlight %}<span class="expl">[코드2. test/exmaple1.html]</span>

{% highlight html linenos %}
<html>
  <head>
  </head>
  <body>
    <h1>example 2</h1>
  </body>
</html>
{% endhighlight %}<span class="expl">[코드3. test/exmaple2.html]</span>

{% highlight html linenos %}
<html>
  <head>
  </head>
  <body>
    <h1>example 3</h1>
  </body>
</html>
{% endhighlight %}<span class="expl">[코드4. test/exmaple3.html]</span>

이제 모든 코드의 작성이 끝났습니다. test/index.html 파일을 더블클릭해 브라우저에 띄운 후 개발자도구의 네트워크 패널을 확인해보면 아래와 같습니다.

![]({{ "/img/react/react-router-01.png" | absolute_url }})<span class="expl">[그림1. test/index.html 개발자도구>네트워크 패널]</span>

서버에 index.html 파일을 요청했고 브라우저는 정상적으로 응답을 받아 알맞게 화면을 구성했습니다. 그 상태로 화면의 ```example 1```을 눌러봅니다. 

<figure>
    <iframe src="../../../../../iframe/react/react-router-02.html" width="100%"></iframe>
</figure>
<span class="expl">[화면2. test/example1.html]</span>

앗, 화면 내용이 example1.html 파일에 작성한 내용으로 바뀌었네요! 그렇다면 개발자도구의 네트워크 패널은 어떻게 바뀌었을까요?

![]({{ "/img/react/react-router-02.png" | absolute_url }})<span class="expl">[그림2. test/example1.html 개발자도구>네트워크패널]</span>

기존에 index.html 파일을 요청한 내역은 사라졌네요. 아하, <span class="text-red">**example1.html 파일을 서버에 ```새로``` ```요청```한 것이군요.**</span> 다른 링크 example2, example3을 눌러도 결과는 마찬가지입니다. 

## 클라이언트 사이드 렌더링(CSR)
같은 내용을 리액트로 구현해 차이를 살펴보겠습니다. 다소 복잡할 수 있으니 잘 따라해보세요. <span class="text-through">일단은 왜 이렇게 해야하는지 몰라도 됩니다. 그냥 따라해보세요.</span>

containers/App/index.js 파일을 아래와 같이 수정합니다.
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
{% endhighlight %}<span class="expl">[코드5. react-boilerplate/containers/App/index.js]</span>

containers/HomePage/index.js 파일을 아래와 같이 수정합니다.
{% highlight javascript linenos %}
import React from 'react';
import { NavLink } from 'react-router-dom';

export default class HomePage extends React.PureComponent {
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

{% endhighlight %}<span class="expl">[코드6. react-boilerplate/containers/HomePage/index.js]</span>

containers 폴더에 Example1 폴더를 만들고 index.js 파일을 추가합니다. containers/Example1/index.js 파일 내용은 아래와 같습니다.
{% highlight javascript linenos %}
import React from 'react';

export default class Example1 extends React.Component {
  render() {
    return (
      <h1>example 1</h1>
    );
  }
}

{% endhighlight %}<span class="expl">[코드7. react-boilerplate/containers/Example1/index.js]</span>

같은 방식으로 containers 폴더에 Example2, Example3 폴더를 만들고 내부 index.js 파일을 만들어 아래의 내용을 각각 추가합니다.
{% highlight javascript linenos %}
import React from 'react';

export default class Example2 extends React.Component {
  render() {
    return (
      <h1>example 2</h1>
    );
  }
}
{% endhighlight %}<span class="expl">[코드8. react-boilerplate/containers/Example2/index.js]</span>

{% highlight javascript linenos %}
import React from 'react';

export default class Example3 extends React.Component {
  render() {
    return (
      <h1>example 3</h1>
    );
  }
}
{% endhighlight %}<span class="expl">[코드9. react-boilerplate/containers/Example3/index.js]</span>

터미널 창에 npm start 명령어를 입력하고 localhost:3000으로 접속합니다. 브라우저 화면은 <a href="#screen-1">[화면1. test/index.html]</a>과 같습니다. 네트워크 패널도 큰 차이는 없습니다.

![]({{ "/img/react/react-router-03.png" | absolute_url }})<span class="expl">[그림3. react-boilerplate/containers/HomePage/index.js 개발자도구>네트워크패널]</span>

하지만, 화면의 example1을 누르고 네트워크 패널을 확인해 보세요!

![]({{ "/img/react/react-router-03.png" | absolute_url }})<span class="expl">[그림4. react-boilerplate/containers/Example1/index.js 개발자도구>네트워크패널]</span>

오잉? 그대로입니다. 즉, url이 바뀌었는데, 새로운 페이지를 렌더링했는데 네트워크 패널은 변하지 않았습니다. <span class="text-red">**새로운 페이지를 요청하지도 않았는데 새로운 페이지가 렌더링되었습니다. 새로운 페이지가 렌더링 되었는데 새로운 페이지가 요청이 된 적은 없습니다.**</span> 이것이 바로 ```클라이언트 사이드 렌더링(이하 CSR)```입니다.

## CSR의 원리
어떻게 해서 이게 가능한 것일까요? 일단 리액트에서는 이렇게 동작합니다.

<pre class="pre-blue">
  1. localhost:3000으로 접속하는 순간, 브라우저는 app/index.html 파일을 서버에 요청합니다. 
  : 이때 요청하는 내용은 페이지 레이아웃, CSS파일, JS파일 등입니다. 
  : 따라서 아직 내부 div#app에는 어떤 내용도 들어있지 않습니다.

  2. 웹팩은 app/app.js 파일에 접근해 앱의 모든 파일을 한데 묶어(packaging) main.js 파일을 만듭니다.
  3. 웹팩이 만든 main.js 파일은 app/index.html 파일의 body 내부 맨 마지막에 script로 감싸져 inject 됩니다.
  4. 브라우저가 app/index.html 파일을 해석하다가 만난 main.js 파일이 실행됩니다. 
  5. main.js는 어떤 내용을 렌더링할지 결정하기 위해 서버에 다시 한번 request를 날립니다.
  6. 렌더링 되는 내용은 div#app 내부에 포함됩니다. 
  7. 새로운 페이지 요청이 들어오면 다시 5번으로 이동하며 반복됩니다.
</pre>

즉, <span class="text-red">어떤 내용이 렌더링 될지는 서버가 아닌 js(클라이언트 사이드)가 결정</span>하며 리액트에서는 ```리액트 라우터```가 그 역할을 담당합니다. 

## CSR vs SSR
그렇다고 항상 CSR이 SSR보다 뛰어난 것은 아닙니다. 왜냐하면 CSR 방식은 SSR 방식에 비해 맨 처음 페이지를 로딩(```initial loading```)하는데 시간이 더 오래걸리기 때문입니다. 

앞서 설명한 바와 같이 SSR 방식에서는 test/index.html 파일을 로드하기 위해 HTTP request를 1번 날립니다. 하지만 CSR 방식에서는 app/index.html 파일을 로드하기 위한 HTTP request 외에 main.js 파일을 실행하면서 또 한번의 HTTP request를 날립니다. HTTP request 1번 더 한다고 그렇게 느려지냐 할 수 있겠지만, 프레임워크 라이브러리까지 생각하면 실제로는 HTTP request를 2번 더 날리는 셈입니다. 이는 앞서 우리가 작성한 예제에서도 쉽게 확인해볼 수 있습니다.

![]({{ "/img/react/react-router-01.png" | absolute_url }})<span class="expl">[그림5. test/index.html 개발자도구>네트워크패널]</span>

![]({{ "/img/react/react-router-03.png" | absolute_url }})<span class="expl">[그림6. react-boilerplate/containers/HomePage/index.js 개발자도구>네트워크패널]</span>

그림5에서는 HTTP request 요청이 1번인데 비해 그림6에서는 무려 3번 이상인 것을 확인할 수 있습니다. 여기서 끝이 아닙니다. js를 파싱하는 일은 생각보다 느립니다. 특히 모바일 웹에서, 인터넷 속도가 느린 환경에서는 그 차이가 더 큽니다. 게다가 main.js 파일의 request 요청은, 도큐먼트가 모두 로드된 이후 가능하기 때문에 initial loading은 더 길어질 수 밖에 없습니다.

이쯤되면 그래서 CSR 방식과 SSR 방식 중에 어떤 것을 택해야 하는지 멘붕이 옵니다. 정답은 없습니다. 개발하고자 하는 프로젝트의 유형에 따라 맞는 방식이 있기 때문이죠. 참고로 우리가 현재 공부하고 있는 리액트는 CSR과 SSR이 혼재되어있습니다. 더 정확한 내용은 다음 글 <a href="https://mymusese.github.io/blog/2018/01/14/react-router-02" target="_blank">04. 리액트 라우터 - 2</a>를 통해 알아보도록 하겠습니다.

## 참고링크(Reference)
<a href="http://openmymind.net/2012/5/30/Client-Side-vs-Server-Side-Rendering/" target="_blank">Client-Side vs. Server-Side Rendering</a>

<a href="https://medium.freecodecamp.org/what-exactly-is-client-side-rendering-and-hows-it-different-from-server-side-rendering-bd5c786b340d" target="_blank">Client-side rendering vs. server-side rendering</a> by Adam Zerner



