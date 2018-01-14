---
layout: post
title: 02. 리액트 폴더구조와 기본 동작 원리
category: react
subtitle: 다소 복잡한 리액트 보일러플레이트의 구조를 파악한 후 기본 동작 원리를 이해해보세요.
date: 2018-01-06
---

코드를 작성하기에 앞서 다소 복잡한 리액트 보일러플레이트의 구조를 먼저 파악해야합니다. 대체 어디에 어떤 코드를 작성해야 되는지, 어떤 원리로 화면에 코드가 반영되는지 무턱대고 접근했다가는 빠른 포기를 경험하게 됩니다.

## 리액트 보일러플레이트 구조

{% highlight javascript linenos %}
react-boilerplate
/.github
/app
    /components
    /containers
    /images
    /tests
    /translations
    /utils
    .htaccess
    .nginx.conf
    app.js
    configureStore.js
    global-styles.js
    i18n.js
    index.html
    manifest.json
    reducers.js
/docs
/internals
/node_modules
/server // 내부 port.js 파일에서 port number를 변경할 수 있습니다.
.editorconfig
.gitattributes
.gitignore
.travis.yml
appveyor.yml
Changelog.md
CODE_OF_CONDUCT.md
CONTRIBUTING.md
LICENSE.md
package.json
README.md
yarn.lock
{% endhighlight %}

수 많은 폴더와 파일이 존재해 다소 복잡해 보이지만, 우리는 일단 ```app``` 폴더, 특히 그 내부 ```components```와 ```containers```의 역할이 ```리액트```의 핵심입니다. 나머지는 국가별 언어설정, 개발 환경에 필요한 웹팩설정, 테스트코드 작성, action과 reducer 등을 위한 파일입니다.

## 리액트는 컴포넌트 기반 라이브러리
리액트는 ```component 기반 라이브러리```입니다. 모든 것은 component 단위로 구성됩니다. 과거엔 하나의 페이지를 하나의 html 파일로 만들었습니다. 그러다보니 페이지의 내용에 따라 html 코드는 수없이 길어질 수 밖에 없었습니다. 유지보수의 어려움이 컸죠. 하지만 리액트는 이를 여러 개의 컴포넌트로 분리한 후 한 파일에 모두 import해서 사용하는 방식입니다. 간단한 예제를 통해 이를 확인해보겠습니다. 예를 들어 아래와 같은 index.html 파일이 있다고 생각해봅시다.

{% highlight html linenos %}
<html>
    <head></head>
    <body>
        <h1>Hello World</h1>
        <button>Click!</button>
    </body>
</html>
{% endhighlight %}

위 코드를 작성한 후 브라우저에서 확인해보면 아래와 같습니다.

<figure>
    <iframe src="../../../../../iframe/react/react-structure-01.html" width="100%"></iframe>
</figure>

같은 내용을 리액트 컴포넌트로 작성해봅니다. 아래의 내용을 ```app > containers > HomePage > index.js``` 파일에 복사/붙여넣기 후 저장합니다. (기존에 리액트 보일러플레이트가 작성한 내용을 지우거나 주석처리한 후 아래의 내용을 복사/붙여넣기 합니다.)

{% highlight javascript linenos %}
import React from 'react';

export default class HomePage extends React.PureComponent { 
  render() {
    return (
      <div>
        <h1>Hello World</h1>
        <button>Click!</button>
      </div>
    );
  }
}
{% endhighlight %}

브라우저를 열고 localhost:3000으로 접속해보면 같은 내용을 확인할 수 있습니다. (간혹 Hello World 글자의 위아래 간격과 Click! 버튼의 디자인이 다를 수 있습니다. 이는 리액트 보일러플레이트가 sanitize.css를 적용하고 있기 때문입니다.)

위 리액트 코드가 의미하는 바는 다음과 같습니다. 

{% highlight javascript linenos %}
import React from 'react'; // 리액트 라이브러리(react)를 불러(import)옵니다.

export default class HomePage extends React.PureComponent {
  // HomePage라는 이름의 리액트 컴포넌트 클래스(지금은 PureComponent가 뭔지 몰라도 됨, 또는 리액트 컴포넌트 타입)를 선언합니다.
  // 다른 컴포넌트에서 HomePage를 불러(import) 사용할 수 있도록 추출(export)합니다. 현재는 default의 의미를 몰라도 됩니다.

  render() { // HomePage 컴포넌트가 불렸을때  (예) <HomePage />) 아래의 내용(리액트 엘리먼트)이 렌더링 됩니다.

    // 아래의 내용은 HomePage 컴포넌트가 렌더링할 리액트 엘리먼트로 JSX syntax를 사용합니다.
    // React 16 이하 버전을 사용하고 있다면, 반드시 하나의 엘리먼트로 감싸진 형태여야 합니다.
    // 예) return (<div></div>);는 되지만 return(<div></div><div></div>);는 허용되지 않습니다.
    return (
      <div>
        <h1>Hello World</h1>
        <button>Click!</button>
      </div>
    );
  }
}
{% endhighlight %}

## 그럼 리액트는 index.html 파일이 없나요?
> - localhost:3000에 접속합니다. 
> - 개발자 도구(alt + command + i)를 켠 후 ```network``` 패널을 선택합니다. 
> - 왼쪽 Name 컬럼에서 ```localhost```(비어있다면 새로고침)를 클릭합니다. 
> - 오른쪽 메뉴(Header / Preview / Response / Timing) 중 ```Response```를 누르면 아래 그림과 같은 화면을 볼 수 있습니다.

![]({{ "/img/react/react-structure-01.png" | absolute_url }})

위 Response 내용은 ```app/index.html``` 파일 내용과 같습니다.

## 리액트는 SPA(Single Page Application)
리액트는 SPA(Single Page Application)입니다. 쉽게 말해 url이 바뀔때마다 서버에서 완전히 새로운 페이지를 렌더링 하는 것이 아니라, app/index.html 페이지 하나(```Single Page```)만 로드한 합니다. 이후 ```리액트 라우터(react router)```가 어떤 내용을 렌더링 할지 결정합니다. 렌더링 되는 내용은 ```웹팩```이 한데 묶어 작은 ```js 파일```로 만듭니다. 그렇게 만들어진 js 파일은 app/index.html 내부 script 태그를 통해 자동으로 추가됩니다. 즉, 리액트의 동작 방식을 정리해보면 다음과 같습니다.

<pre class="pre-blue">
  1. localhost:3000으로 접속하면 브라우저는 app/index.html 파일을 해석합니다.
  2. 이와 동시에, 웹팩은 app/app.js 파일을 통해 앱에 접근해 앱의 모든 내용을 하나의 작은 js 파일로 만듭니다.
  3. 그 js 파일을 app/index.html 내부 div#app에 script 형태로 추가합니다.
  4. 브라우저는 그 내용을 읽어 리액트 프로젝트를 렌더링합니다.
</pre>

## app/app.js
그렇다면 ```app/app.js``` 파일은 어떤 내용을 담고 있을까요? ```app/app.js``` 파일은 쉽게 말해 앱의 원활한 동작을 위한 글로벌 설정들을 포함하고 있습니다. 이 중 핵심 몇 가지만 간략하게 설명해보았습니다.

{% highlight javascript linenos %}
import 'babel-polyfill'; // generator functions, Promise 등을 사용할 수 있게 하고 ES6 이상의 문법을 ES5 문법으로 변형해줍니다.

import App from 'containers/App'; // app/containers/App/js 파일을 불러옵니다.

import '!file-loader?name=[name].[ext]!./images/favicon.ico'; // favicon을 app/images에서 불러옵니다.
import './global-styles'; // global css를 불러옵니다.

const initialState = {};
const history = createHistory(); // 앱이 방문한 모든 페이지에 대한 기록을 갖고 있는 오브젝트를 생성합니다.
const store = configureStore(initialState, history); 
const MOUNT_NODE = document.getElementById('app'); // document는 app/index.html가 기준입니다.

const render = (messages) => {
  ReactDOM.render(
    <Provider store={store}>
      <LanguageProvider messages={messages}>
        <ConnectedRouter history={history}>
          <App />
        </ConnectedRouter>
      </LanguageProvider>
    </Provider>,
    MOUNT_NODE
  );
};
{% endhighlight %}


## ReactDom.render()
ReactDom.render(element, container)는 element를 container 내부에 렌더링합니다. 쉽게 말해, ```<Provider />``` 컴포넌트를 app/index.html 파일 속 <div id="app"></div> 엘리먼트 내부에 렌더링하는 것이죠. 결과는 아래와 같을 것입니다.

{% highlight html linenos %}
<!doctype html>
<html lang="en">
  <head>
    <!-- The first thing in any HTML file should be the charset -->
    <meta charset="utf-8">
    <!-- Make the page mobile compatible -->
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- Allow installing the app to the homescreen -->
    <link rel="manifest" href="manifest.json">
    <meta name="mobile-web-app-capable" content="yes">
    <title>React.js Boilerplate</title>
  </head>
  <body>
    <!-- Display a message if JS has been disabled on the browser. -->
    <noscript>If you're seeing this message, that means <strong>JavaScript has been disabled on your browser</strong>, please <strong>enable JS</strong> to make this app work.</noscript>

    <!-- The app hooks into this div -->
    <div id="app">
      <Provider store={store}>
        <LanguageProvider messages={messages}>
          <ConnectedRouter history={history}>
            <App />
          </ConnectedRouter>
        </LanguageProvider>
      </Provider>
    </div>
    <!-- A lot of magic happens in this file. HtmlWebpackPlugin automatically includes all assets (e.g. bundle.js, main.css) with the correct HTML tags, which is why they are missing in this HTML file. Don't add any assets here! (Check out webpackconfig.js if you want to know more) -->
  </body>
</html>

{% endhighlight %}

사실 ReactDom.render() 함수가 렌더링하는 엘리먼트는 ```리액트 root 엘리먼트```입니다. 리액트 보일러플레이트는 리액트 root 엘리먼트를 app/containers/App/index.js에 선언된 ```<App />``` 컴포넌트로 정의하고 있습니다. ```<App />```을 감싸고 있는 ```<Provider />```, ```<LanguageProvider />``` 등은 각각 앱과 ```리덕스```(```redux```, 현재는 몰라도 됨)를 연결하고 국가별 언어설정이 가능하게 하는 역할을 하는데 현재는 몰라도 됩니다.

요약해보면,
<pre class="pre-blue">
  1. 터미널에 npm start 명령어를 입력합니다.
  2. localhost:3000으로 접속합니다.
  3. 웹팩은 app/containers/App/index.js에 선언된 App 컴포넌트를 렌더링합니다.
  4. 그 내용은 app/index.html에 추가됩니다.
  5. 브라우저는 app/index.html 파일 로드합니다.
  6. 결과적으로 화면엔 App 컴포넌트에 작성된 내용이 보여집니다.
</pre>

다음 글에서는 url에 따라 다른 컴포넌트를 렌더링하게 하는 ```리액트 라우터```에 대해 살펴볼 예정입니다.



