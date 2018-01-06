---
layout: post
title: HTTP vs HTTPS
category: network
subtitle: HTTP와 HTTPS의 차이점은 무엇이며 HTTPS의 암호화 방식은 어떤 원리일까?
date: 2017-12-07
---

### 1. HTTPS의 등장
HTTPS는 온라인 쇼핑, 인터넷 뱅킹 등 보다 강력한 보안을 위해 등장했다. HTTPS는 아래와 같은 보안기술을 가능하게 한다.

> - Authentication:&nbsp; &nbsp; &nbsp; 클라이언트와 서버는 각각 인증된 대상과 통신해야 한다.
> - Integrity: &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 클라이언트와 서버가 주고 받는 데이터는 위조되지 않아야 한다.
> - Confitentiality: &nbsp; &nbsp; &nbsp; 클라이언트와 서버간 통신은 도청으로부터 안전해야 한다.
> - Effectiveness:&nbsp; &nbsp; &nbsp; &nbsp; 암호화를 위한 알고리즘은 강력하고 빨라야 한다.


### 2. HTTP vs HTTPS

#### URI SCHEME 
```http://```
```https://```

#### PORT NUMBER (DEFAULT)
```80```
```443```

#### TCP/IP MODEL
HTTPS를 사용하면, 모든 HTTP 요청/응답 메시지가 네트워크로 전송되기 전에 암호화된다. 암호화는 SSL 또는 TLS (아래 그림 (b) 참조)를 이용하여 구현된다.

![](https://www.safaribooksonline.com/library/view/http-the-definitive/1565925092/httpatomoreillycomsourceoreillyimages96902.png){: style="display:block;margin-left:auto;margin-right:auto;"}


<pre class="pre-blue">
	<span>SSL은 무엇인가요?</span>
	응용계층(Application Layer)으로부터 전달된 데이터에 보안성을 제공하기 위해 고안된 프로토콜이다.

	1. 데이터는 먼저 잘게 쪼개진 후(fragmentation) 압축(compression, 선택사항)된다.
	2. 해시 함수와 비밀 키를 이용해 MAC(Message Authentication Code)을 만든다.
	3. 데이터와 MAC은 함께 암호화된다.
	4. 암호화된 데이터에 헤더를 붙여 전송계층(Transport Layer)로 전달한다.
	5. 수신자는 데이터와 MAC을 분리한 후, 2번 과정을 통해 MAC을 만든다.
	6. 전달된 MAC과 만들어진 MAC을 비교해 데이터 인증여부를 판단한다.
</pre>

### 3. 암호화 방법
#### 3-1. 대칭키(Symmetric) 암호화
대칭키 암호화란, 인코딩 할때의 키와 디코딩 할때의 키가 같은 암호화 방식을 말한다. 즉, 송신자(클라이언트)와 수신자(서버)가 동일한 ```비밀키(Secret Key)```로 암호화한다.
하지만, 비밀키를 전달하는 과정에서 노출될 위험이 있어 안전하지 않다. 또한 N개의 클라이언트가 N개의 서버와 대칭키 암호화로 통신하는 경우, 무려 NxN쌍의 비밀키가 필요하다.

#### 3-2. 비대칭키/공개키(Asymmetric) 암호화
반면 비대칭키 암호화란, 인코딩 할때의 키와 디코딩 할때의 키가 다른 암호화 방식을 말한다.
송신자는 수신자의 ```공개키(Public Key)```로 암호화하고, 수신자는 자신의 ```개인키(Private Key)```로 복호화하는 방식이다. 
따라서 수신자의 비밀키는 노출될 위험이 없으며, N개의 개인키와 N개의 비밀키만 요구된다.

하지만 해커가 공개키, 암호화 된 데이터, 암호화 알고리즘 이 모두를 알고 있더라도 복호화를 할 수 없을 만큼 암호화 알고리즘이 강력해야한다. 가장 유명한 알고리즘으로는, MIT에서 만든 ```RSA 알고리즘```이 있다.
알고리즘이 강력한 만큼 계산시간이 다소 오래걸리는 단점이 있다. 따라서 실제로는 대칭키 암호화와 비대칭키 암호화 방식을 섞어서 사용한다. 

예를 들어, TCP 커넥션을 수립할때는 비대칭키 암호를 사용한다. 이후 대칭키를 생성해 생성된 안전한 채널을 통해 교환한 후 데이터를 암호화해 통신하는 방식이 일반적이다.

### 4. 데이터 무결성(Integrity) 검증
우리는 클라이언트와 서버가 주고받는 데이터가 제 3자에 의해 위/변조 되지 않았는지 확인해야할 필요가 있다. 이를 위해 ```다이제스트 인증```방식을 사용한다. 

<pre class="pre-red">
	다이제스트 인증이 데이터 무결성을 검증하기 위한 가장 강력한 프로토콜은 아니다.
	참고서적 'HTTP 완벽 가이드: 웹은 어떻게 동작하는가?'에 따르면, 2014년 기준 다이제스트 인증은 널리 쓰이지 않고 있다.
</pre>

다이제스트 인증을 통해 로그인을 하는 경우를 가정해보자. 클라이언트가 입력한 비밀번호는 네트워크를 통해 서버로 전달되지 않는다.
대신 ```해시 함수```를 통해 생성한 비밀번호 ```요약본(digest)```을 서버로 전달한다.
서버는 데이터베이스에서 해당 아이디에 맞는 비밀번호를 찾아 해시함수로 비밀번호 요약본을 만들고 클라이언트로부터 전달된 요약본과 비교한다.

하지만, 해커는 위 요약본을 가로채 서버에 계속 인증을 요구할 수 있다. 서버는 이를 방지하기 위해 ```난스(nounce)```를 클라이언트에게 넘긴다. 
클라이언트는 비밀번호와 난스를 섞어서 요약본을 만든다. 난스는 약 1ms마다 값이 바뀌기 때문에, 해커가 중간에 요약본을 가로채 서버로 재전송해도 그 사이에 난스 값이 바뀌어서
서버는 유효하지 않은 요약본으로 취급한다.













