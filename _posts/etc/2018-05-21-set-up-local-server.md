---
layout: post
title: 모바일 환경 테스트를 위한 로컬 서버 띄우기
category: etc
subtitle: 모바일 환경에서 보다 정확한 테스팅을 위해 로컬 서버를 띄워봅시다.
date: 2018-05-21
---

해당 글은 <a href="https://developer.mozilla.org/en-US/docs/Learn/Common_questions/set_up_a_local_testing_server" target="_blank">MDN: How do you set up a local web server</a>를 번역 및 요약한 글임을 밝힙니다.

## 우리가 분노할때: `IE` 그리고 `모바일 사파리`
프론트엔드 개발자라면 분노 게이지가 상승하는 순간을 많이 경험합니다. 그중에서도 단연 IE와 모바일 환경(특히 모바일 사파리)을 테스팅할때가 정점이죠. IE 환경에서의 오작동의 원인은 대부분 그냥 해당 css 속성을 IE 브라우저가 지원하지 않는 것이기 때문에 딱히 이렇다 할 해결 방법이 없습니다. 클라이언트가 IE10 이상만 커버하겠다면 정말 고맙겠지만...그게 아니라면 prefix를 적절하게 활용하거나 같은 효과를 내는 다른 속성을 사용할 수 밖에 없습니다.

모바일 환경은 그나마 개발이 수월합니다. 문제는 디바이스마다 비율이 다르기 때문에 미묘한 간격, 크기 차이는 디자이너의 눈에 거슬릴 수 있습니다. 그래도 media 쿼리가 있으니까 다행입니다. 귀찮아도 방법은 있으니까요!

## 발암 유발 모바일 사파리
<span class="text-red">하지만 테스팅에서 뒷통수를 맞을 수 있습니다.</span> 브라우저 개발자모드에서 `Device Toolbar` 등으로 테스팅을 할때와 실제 디바이스에서 보이는 화면은 조금 차이가 있기 때문입니다. 예를 들어, 최상위 엘리먼트에 `height: 100%`을 적용<span class="text-red">(height을 함부로 건드리는 것은 위험한 행동입니다...)</span>하면 개발자모드에서는 원하는 결과가 잘 나오지만, 모바일 사파리에서는 스크롤이 생겨버립니다. 모바일 사파리의 경우 디바이스 상단 `address 바`와 `하단 menu 바`가 존재하기 때문인데, 개발자모드에서는 이를 반영해 결과를 보여주지 않습니다. 결국 실제 디바이스에서 확인하는 것이 제일 정확한 테스팅입니다.

## 귀찮아하지말고 `로컬 웹 서버`를 띄우자
리액트 등, 기타 JS 프레임워크를 사용하면 프레임워크가 로컬 웹 서버를 띄워주기 때문에 딱히 할 일이 없습니다. 그냥 `본인이 연결한 IP주소:포트번호`로 접속하면 디바이스에서 바로 확인해볼 수 있습니다. 하지만 프레임워크를 사용하지 않는 경우엔(단순 마크업만 하는 경우 등) 직접 로컬 웹 서버를 띄워야합니다. 번거롭더라도 미래에 닥칠 재앙을 사전에 차단합시다.

## 간단한 HTTP 로컬 웹 서버 띄우기
1. 파이썬을 설치합니다. 리눅스나 맥 OS X 유저는 이 과정을 생략해도 됩니다.
2. 터미널을 열고 다음 명령어(설치된 파이썬의 버전 확인)를 통해 파이썬이 잘 설치되었는지 확인합니다.

<pre class="pre-blue">python -V</pre>

{:start="3"}
3. 로컬 웹 서버를 띄울 프로젝트 파일로 이동합니다.

<pre class="pre-blue">cd 프로젝트 파일 위치</pre>

{:start="4"}
4. 아래 명령어를 통해 로컬 웹 서버를 띄웁니다.

<pre class="pre-blue">
# 파이썬 버전이 3.X보다 높은 경우
python -m http.server 

# 파이썬 버전이 2.X보다 높은 경우
python -m SimpleHTTPServer
</pre>

{:start="5"}
5. `localhost:8000`(default 포트번호가 8000)로 접속합니다. 만약 이미 8000번 포트가 사용중이라면, 다음 명령어를 통해 특정 포트번호를 지정할 수 있습니다.

<pre class="pre-blue">
# 파이썬 버전이 3.X보다 높은 경우
python -m http.server 7800

# 파이썬 버전이 2.X보다 높은 경우
python -m SimpleHTTPServer 7800
</pre>

## 서버사이드 언어로 작성된 파일을 로컬 웹 서버로 띄우기
`SimpleHTTPServer` 모듈은 매우 유용하지만, Python, PHP 또는 JS로 작성된 코드를 돌릴 순 없습니다. 예제를 통해 확인해보겠습니다. example이라는 폴더를 만들고 index.html 파일과 action.php 파일을 만든 후, 각각 아래와 같이 코드를 입력해 저장합니다.

<span id="example1"></span>
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
{% endhighlight %}<span class="expl">[예제1. example/index.html]</span>

{% highlight html linenos %}
<html>
  <head></head>
  <body>
    이름: <?php echo $_GET['username'] ?>
  </body>
</html>
{% endhighlight %}<span class="expl">[예제1. example/action.php]</span>

index.html 파일을 브라우저에 띄워보면 아래와 같은 화면을 볼 수 있습니다.

![]({{ "/img/etc/web-security-01.png" | absolute_url }})
<span class="expl">[그림1. example/index.html]</span>

이름을 입력하고 제출 버튼을 눌러봅니다. 잉? 뜬금없이 action.php 파일이 다운로드 됩니다. (로컬 환경에서는 action.php 파일 코드가 그대로 화면에 출력됨) <span class="text-red">PHP Preprocessor가 없기 때문</span>(쉽게 말해 `SimpleHTTPServer` 모듈이 PHP코드를 해석할 수 없음)인데요. 이렇듯 앞서 설명한 바와 같이 단순 html 파일 외에 Python, PHP, JS등으로 작성된 코드를 로컬에서 돌리려면, 각 언어에 맞는 모듈이 필요합니다.

### 1. Python 서버사이드 코드

파이썬 서버사이드 코드를 돌리려면, <a href="https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django" target="_blank">Django 웹 프레임워크</a>와 같은 파이썬 웹 프레임워크를 사용해야합니다. Flask는 보다 가볍기 때문에 Django의 대안으로 떠오르고 있는데요. Flask를 통해 파이썬 코드를 돌리려면 먼저 `Python3` 그리고 `PIP3` 를 설치(자세한 내용은 <a href="https://developer.mozilla.org/en-US/docs/Learn/Server-side/Django/development_environment#Installing_Python_3" target="_blank">Installing Python3</a> 참조)해야 합니다. 이후 `pip3 install flask` 명령어를 통해 Flask를 설치합니다. 자 이제 Python Flask 예제를 로컬 웹 서버에 띄울 준비가 끝났습니다. `python3 파일이름.py` 명령어를 입력하고 `localhost:5000`으로 접속합니다.

### 2. JS 서버사이드 코드
Node.js(자바스크립트) 서버사이드 코드를 돌리려면, 프레임워크를 사용해야하는데 <a href="https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs" taget="_blank">Express Web Framework</a>가 가장 유용합니다. 

#### 2-1. NodeJS/NPM 설치
Express 개발 환경을 구축하려면, NodeJS, NPM 등을 설치해야 합니다. 설치여부를 확인하려면 터미널에서 각각 `node -v`, `npm -v`를 입력해봅니다.

#### 2-2. Express Application Generator 설치
NodeJS와 NPM을 설치했다면 `Express Application Generator`를 설치하기 위해 아래와 같은 명령어를 입력합니다. Express Application Generator는 말 그대로 Express 어플리케이션 뼈대를 만들어 주는 툴입니다. 리액트를 개발할때 `create-react-app` 또는 `react-boilerplate`를 clone 받기 위한 사전 작업과 비슷한 개념이라고 할 수 있죠.

<pre class="pre-blue">npm install express-generator -g</pre>

#### 2-3. express 어플리케이션 생성
프로젝트 폴터를 만들 위치로 이동한 후, 아래 명령어를 통해 express 어플리케이션을 만듭니다.

<pre class="pre-blue">
cd Desktop
express example
</pre>

Desktop에 example이라는 이름의 express 어플리케이션이 생성된 것을 확인할 수 있습니다. 폴더 구조는 아래와 같습니다.

```
/bin
/public
/routes
/views
app.js
package.json
```

`package.json` 파일엔 개발에 필요한 `dependencies` 목록이 담겨 있습니다. 아래 명령어를 통해 나머지 dependency들을 설치합니다.

<pre class="pre-blue">
cd example
npm install
</pre>

#### 2-4. 어플리케이션 실행
어플리케이션을 실행하기 위해 각 OS에 맞는 명령어를 아래와 같이 입력합니다.

<pre class="pre-blue">
# 윈도우
SET DEBUG=example:* & npm start

# 리눅스/맥 OS
DEBUG=example:* npm start
</pre>

브라우저를 열고 `http://127.0.0.1:3000`으로 접속합니다. 아래와 같은 default 페이지를 확인할 수 있습니다.

<span id="img2"></span>
![]({{ "/img/etc/set-up-local-server-02.png" | absolute_url }})
<span class="expl">[그림2. express 어플리케이션 default page]</span>

구조를 잠깐 설명하기 위해 `routes/index.js` 파일을 살펴보겠습니다.
{% highlight javascript linenos %}
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});
{% endhighlight %}
<span class="expl">[코드3. example/routes/index.js]</span>

 `127.0.0.1:3000/`로 요청이 들어오면 `views/index.jade` 파일을 응답으로 전달한다는 뜻 입니다. 이때 title값은 `Express`로 전달합니다. `views/index.jade` 파일은 아래와 같습니다.
{% highlight javascript linenos %}
extends layout

block content
  h1= title
  p Welcome to #{title}
{% endhighlight %}
<span class="expl">[코드4. example/views/index.jade]</span>

`views/layout.jade`를 기본으로, 전달 받은 title값을 넣어 아래와 같은 html로 변환합니다.

{% highlight html linenos %}
<!DOCTYPE html>
<html>
  <head>
    <title>Express</title>
    <link rel="stylesheet" href="/stylesheets/style.css">
  </head>
  <body>
    <h1>Express</h1>
    <p>Welcome to Express</p>
  </body>
</html>
{% endhighlight %}
<span class="expl">[코드5. example/views/index.jade를 html로 변환]</span>

따라서 `127.0.0.1:3000/`로 요청을 날리면 <a href="#img2">그림2</a>와 같은 페이지를 확인할 수 있습니다.

### 3. PHP 서버사이드 코드
PHP 서버사이드 코드를 돌리려면, PHP 코드를 해석하기 위한 서버 셋팅이 필요합니다. 로컬 PHP 테스팅을 위해서라면, <a href="https://www.mamp.info/en/downloads/" target="_blank">`MAMP`</a>(맥 또는 윈도우), `AMPPS`(맥, 윈도우, 리눅스) 그리고 `LAMP`(리눅스, Apache, MySQL 그리고 PHP/Python/Perl)등의 옵션이 있습니다. 이는 다소 복잡한데, 위에 소개한 <a href="#example1">예제1</a>처럼 단순한 코드는 `php -S localhost:포트번호` 명령어를 통해 php 코드가 동작하도록 만들 수 있습니다. 터미널을 켜고 예제 폴더로 이동한 다음, `php -S localhost:8000`을 입력합니다. 이름을 적는 란에 `deedee`를 입력하고 제출 버튼을 누르면, PHP 파일이 다운로드 되거나 코드 내용이 그대로 출력되지 않고 아래와 같이 입력한 이름이 정상적으로 출력되는 것을 확인할 수 있습니다.

![]({{ "/img/etc/set-up-local-server-01.png" | absolute_url }})
<span class="expl">[그림2. example/aciton.php]</span>

이번 글에서는 다양한 상황에 맞게 로컬 웹 서버를 띄우는 방법을 알아보았습니다. 요즘엔 대부분 개발자들이 프레임워크를 사용해 개발하기 때문에 로컬 웹 서버를 직접 띄울 일은 별로 없지만, 퍼블리셔 분들이나 가끔 모바일 환경 등의 테스팅이 필요하신 분들은 위 내용을 알아두면 유용하게 사용할 수 있을 것입니다.

## 참고링크(Reference)
<a href="https://developer.mozilla.org/en-US/docs/Learn/Common_questions/set_up_a_local_testing_server" target="_blank">MDN: How do you set up a local testing server?</a>

<a href="https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment" target="_blank">Setting up a Node development environment</a>
