---
layout: post
title: XMLHttpRequest
category: etc
subtitle: Ajax 통신의 원리, XMLHttpRequest 방식에 대해 알아봅시다.
date: 2018-05-21
---

해당 글은 <a href="https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest" target="_blank">MDN: XMLHttpRequest</a>을 번역한 글임을 밝힙니다.

`XMLHttpRequest(이하 XHR)` 객체를 사용하면, 페이지를 새로고침 없이 데이터를 서버에 요청할 수 있어 페이지 일부분만 업데이트 할 수 있습니다. 

이름 자체는 `XML`HttpRequest이지만, 굳이 XML 형식이 아닌 데이터도 서버에 요청할 수 있습니다. 또한 HTTP 프로토콜 외에 file 또는 ftp 프로토콜도 지원합니다.



## 참고링크(Reference)
<a href="https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest" target="_blank">MDN: XMLHttpRequest</a>