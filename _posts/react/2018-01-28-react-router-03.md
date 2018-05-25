---
layout: post
title: 05. 리액트 라우터 - 3. 네비게이션 만들기
category: react
subtitle: 네비게이션 기능을 직접 구현해봅니다.
date: 2018-01-28
---

## 네비게이션(navigation)이란?
우리에게 네비게이션이란, 자동차가 가야할 방향을 알려주는 도구로 친숙히 알려져있습니다. 웹에서 말하는 네비게이션 또한 그 역할이 크게 다르지 않습니다. 웹에서 네비게이션이란, 특정 URL에 대해, 렌더링 될 내용(리액트에서는 컴포넌트)을 연결해주는 기능을 합니다. 꼭 하나의 URL에 대해 하나의 컴포넌트가 연결되는 것은 아닙니다. URL과 컴포넌트는 `1 : N`의 관계며 N은 1 이상의 자연수입니다. 하지만 이번 글에서는 이해를 돕기 위해 1 : 1의 상황만 다루도록 하겠습니다.

네비게이션 컴포넌트를 작성하기에 앞서, 우리가 앞으로 리액트 시리즈를 통해 만들어나갈 앱에 대해 소개하겠습니다. 어떤 앱을 만들면 좋을까 고민하다가 지극히 개인적인 성향을 고려하기로 했습니다. 이 글은 제가 작성하는 것이니까요. 아직 무엇을, 어떻게 만들지는 잘 모르겠지만 한 가지는 확실합니다. 컨텐츠는 바로 `야구`라는 것.

리액트는 다소 복잡한만큼 `로그인 여부`, `유저의 권한`, `스크롤 동작` 등을 고려해 앱의 구조를 잡아야 합니다. 일단 로그인 여부에 따라 초기 렌더링 될 페이지가 달라질 것 같지는 않습니다. 그래서 로그인 여부는 나중에 고려하도록 합니다. 유저의 권한 또한 대부분 동일 할 것으로 판단됩니다. 마지막으로 스크롤 동작은 야구에 대한 내용이 뉴스든 데이터든 다소 많을 것으로 고려되어 꽤 신경쓰이는 대목입니다. 네비게이션을 상단 헤더에 위치시킬것인가 왼쪽 또는 오른쪽에 위치시킬것인가에 따라 또 달라집니다. 상단 헤더에 위치시킨다면 크게 신경 쓸 필요 없지만 왼쪽 또는 오른쪽에 위치한다면 네비게이션과 컨텐츠의 스크롤의 동작이 따로 분리되어야 해서 다소 복잡해집니다.

그렇다면 단순히 "개발의 편리함을 위해 네비게이션을 상단에 위치시킬것인가?"라고 묻는다면 케바케입니다. 만약 네비게이션의 하위 메뉴가 많을 경우, 상단 헤더보단 왼쪽 또는 오른쪽에 위치시키는 것이 좋다고 생각합니다. 네비게이션 메뉴가 펼쳐지면 내용을 가릴 수 밖에 없기 때문이죠. 그렇다고 컨테이너를 밀어내자니 너무 많은 자원이 소모됩니다. 네비게이션 하위 메뉴가 없거나 짧은 경우엔 상단 헤더에 위치시켜도 무방합니다. 야구는 팀이 많기 때문에 네비게이션 하위 메뉴가 많을 것으로 예측됩니다. 네비게이션은 왼쪽에 위치시키도록 합니다.

자 그럼 큰 구조는 잡혔습니다. 아래 사진처럼 웹에서 페이스북 로그인했을때 나오는 화면과 똑같은 구조라고 생각하시면 됩니다.

![]({{ "/img/react/react-router-05.png" | absolute_url }})<span class="expl">[그림1. 기본 앱 구조 헤더, 네비게이션 그리고 컨테이너]</span>

1번 영역이 헤더, 2번 영역이 네비게이션 그리고 3번 영역이 컨텐츠가 들어갈 컨테이너입니다.

## 최상단 App 컴포넌트 구성하기
App 컴포넌트는 리액트 앱의 최상단 컴포넌트로 무조건 렌더링됩니다. 그래서 <span class="text-red">App 컴포넌트에는 페이지마다 공통적으로 들어가는 컴포넌트가 포함</span>됩니다. 우리의 앱에는 `헤더`, `네비게이션` 그리고 기호에 따라 `푸터`가 모든 페이지에 공통적으로 포함되는 컴포넌트이기 때문에 app/App/index.js는 아래와 같이 구성됩니다.

{% highlight javascript linenos %}
/**
 *
 * App.js
 *
 * This component is the skeleton around the actual pages, and should only
 * contain code that should be seen on all pages. (e.g. navigation bar)
 *
 * NOTE: while this component should technically be a stateless functional
 * component (SFC), hot reloading does not currently support SFCs. If hot
 * reloading is not a necessity for you then you can refactor it and remove
 * the linting exception.
 */

import React from 'react';
import { Switch, Route } from 'react-router-dom';

import Header from 'components/Header';
import Navigation from 'components/Navigation';
import Contents from 'components/Contents';
import NotFoundPage from 'containers/NotFoundPage/Loadable';

export default function App() {
  return (
    <Wrapper>
      <Header />
      <Container>
        <Navigation />
        <Contents />
      </Container>
    </Wrapper>
  );
}

const Wrapper = styled.div``;
const Container = styled.div``;

{% endhighlight %}<span class="expl">[코드1. app/App/index.js]</span>

## 참고링크(Reference)
<a href="https://reacttraining.com/react-router/web/guides/philosophy" target="_blank">리액트 라우터 돔 공식문서</a>





