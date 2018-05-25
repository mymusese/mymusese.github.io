---
layout: post
title: 서버로부터 데이터 요청하는 원리
category: etc
subtitle: 서버로부터 데이터를 요청하고 받아오는 기본 원리에 대해 알아봅시다.
date: 2018-05-21
---

해당 글은 <a href="https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data" target="_blank">MDN: Fetching data from the server</a>을 번역한 글임을 밝힙니다.


웹 사이트와 어플리케이션이 흔히 하는 일은 서버로부터 데이터를 요청하는 것입니다. 하지만 페이지의 일부분을 업데이트 하기 위해 전체를 리로딩 해야 한다고 생각해보세요. 웹이 무거워질수록 굉장히 크리티컬한 문제가 됩니다. 아래 글은 새로고침 없이 페이지 일부를 업데이트하기 위해 서버로부터 데이터를 가져오는 방법에 대한 기술을 소개합니다. 

### 문제는 무엇?
웹이 페이지를 로딩하는 방식은 생각보다 단순했습니다. 클라이언트는 웹 서버에 해당 페이지를 요청하는 request를 날리고, 웹 서버는 클라이언트에게 요청한 웹 페이지를 그리기 위해 필요한 asset들을 response로 보냅니다. 브라우저는 받은 내용을 그립니다. 

<img src="https://mdn.mozillademos.org/files/6475/web-site-architechture@2x.png" alt="" style="width: 560px;" />

이러한 방식은, 페이지의 일부분만을 업데이트 하고 싶을때 문제에 부딪힙니다. 이런 경우 위와 같은 방식을 사용한다면, 클라이언트는 웹 서버에게 페이지 전체를 요청하고 브라우저는 웹 서버로부터 받은 내용을 다시 그려야합니다.

### Ajax 통신
이러한 문제를 해결하기 위해 웹 페이지가 데이터의 일부분(예를 들어, HTML, XML, JSON 또는 텍스트)만 요청하도록 허용하는 기술들이 생겨나기 시작했습니다. 그 중 XMLHttpRequest API 또는 Fetch API를 사용하는 방법이 가장 대표적입니다.

<img src="https://mdn.mozillademos.org/files/6477/moderne-web-site-architechture@2x.png" alt="" style="width: 560px;" />

Ajax는 Asynchronous JavaScript and XML의 약자로, XMLHttpRequest를 사용해 데이터를 요청하는 기술입니다. Ajax 통신을 사용하면 페이지 전체를 다시 그리지 않기 때문에 페이지 업데이트가 빠릅니다.
또한 업데이트가 필요한 일부 데이터만 서버에 요청하기 때문에 http 통신시 bandwidth가 줄어듭니다. 테스크탑 환경에서는 큰 차이가 없지만, 모바일 환경에서는 훨씬 효율적입니다. 특히, 인터넷 속도가 느린 환경에서는 그 차이가 두드러집니다.

속도를 더 높이기 위해 이미지 등 일부 asset 들을 유저의 컴퓨터에 저장해놓는 경우도 있습니다. 페이지를 처음 로드할때만 가져오고, 해당 내용이 업데이트 되지 않는 이상 다시 가져오지 않습니다.

<img src="https://mdn.mozillademos.org/files/6479/web-app-architecture@2x.png" alt="" style="width: 560px;" />

### Ajax request
간단한 예제를 통해 Ajax 통신으로 서버로부터 데이터를 주고받는 일련의 과정을 살펴보겠습니다. 서버로부터 텍스트파일을 요청하고 그 내용을 컨텐츠 영역에 보여주는 기능을 구현해보겠습니다. (원래는 PHP, Python, Node등과 같은 서버사이드 언어로 실제 db로부터 데이터를 받아와야 하지만, Ajax 동작을 살펴보기 위해 간단한 예제를 구성해보았습니다.)

#### 1.XMLHttpRequest(이하 XHR) 방식

1. 시작하기에 앞서, 새로운 폴더 안에 5개의 파일(<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/fetching-data/ajax-start.html" target="_blank">ajax-start.html</a>, <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/fetching-data/verse1.txt" target="_blank">verse1.txt</a>, <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/fetching-data/verse2.txt" target="_blank">verse2.txt</a>, <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/fetching-data/verse3.txt" target="_blank">verse3.txt</a>, <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/fetching-data/verse4.txt" target="_blank">verse4.txt</a>)을 만듭니다.

2. script 엘리먼트 내부에 아래와 같이 코드를 추가합니다.

{% highlight html linenos %}
<script>
  var verseChoose = document.querySelector('select'); // select 엘리먼트를 변수에 저장한다.
  var poemDisplay = document.querySelector('pre'); // pre 엘리먼트를 변수에 저장한다.

  verseChoose.onchange = function() { // select의 값이 변하면
    var verse = verseChoose.value;
    updateDisplay(verse); // updateDisplay 함수를 호출하고 그 값을 parameter로 넘긴다.
  };

  function updateDisplay(verse) {

  }
</script>
{% endhighlight %}

4. updateDisplay 함수 내부에서 선택된 값(verse)을 request 형식에 맞게 변형합니다. 예를 들어 Verse 1을 선택하면 verse1.txt 파일을 요청하기 위해 아래와 같은 코드가 필요합니다.
{% highlight js linenos %}
  function updateDisplay(verse) {
    verse = verse.replace(' ', ''); // space를 없앤다 : Verse1
    verse = verse.toLowerCase(); // 모두 소문자로 바꾼다 : verse1
    var url = verse + '.txt'; // .txt를 붙인다 : verse1.txt
  }
{% endhighlight %}

5. XHR request를 위해 새로운 XMLHttpRequest 객체를 생성합니다.
{% highlight js linenos %}
  var request = new XMLHttpRequest();
{% endhighlight %}

6. `open()` 메소드를 통해 HTTP request를 보낼때 method와 url을 설정합니다.
{% highlight js linenos %}
request.open('GET', url);
{% endhighlight %}

7. response type을 text로 설정합니다. (사실 XHR의 default response type은 text이기 때문에 따로 설정하지 않아도 됩니다.)
{% highlight js linenos %}
request.responseType = 'text';
{% endhighlight %}

8. 네트워크로부터 리소스를 가져올땐 비동기 방식이기 때문에 응답이 올때까지 기다려야합니다. 응답이 오기 전에 해당 리소스로 무언가를 하려고 하면 에러가 발생합니다. XHR은 `onload` event handler를 통해 응답이 왔다는 것을 감지합니다.
{% highlight js linenos %}
  request.onload = funciton() {
    poemDisplay.textContent = request.response; // 응답이 오면, pre 엘리먼트 내부에 해당 리소스를 추가합니다.
  };
{% endhighlight %}

9. `send()` 메소드를 통해 request를 호출합니다.
{% highlight js linenow %}
   request.send();
{% endhighlight %}

10. 페이지가 처음 로딩되었을때 


자 이제 끝났습니다. ajax-start.html 파일을 더블클릭해 브라우저에 띄운 후, `select 값(Choose a verse 옆 드롭다운)`을 바꿔보세요. 선택한 select 값을 이름으로 갖는 파일의 내용이 하단 하얀 박스 내부에 표시되는지 확인합니다. 

## 참고링크(Reference)
<a href="https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data" target="_blank">MDN: Fetching data from the server</a>