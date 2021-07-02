---
layout: post
title:  "HTTPS 동작 과정 (대칭키, 공개키, SSL, TLS, CA)"
subtitle: "HTTPS 동작 과정 (대칭키, 공개키, SSL, TLS, CA)"
categories: development
tags: security
comments: false
---

# HTTPS의 등장 배경

인터넷에서 귀중한 내 정보를 어디론가 전달하거나, 열람하는 경우가 많다 예를 들면 포털 사이트에 내 ID와 비밀번호를 입력하고 로그인을 하거나, 거래 은행 웹 사이트에 들어가서 내 계좌 정보를 조회하는 경우 등이 있다. 만약 인터넷에서 누군가가 귀중한 정보를 송수신 하는 동안에 그 송수신하는 정보를 몰래 감시해서, 나의 중요 정보를 몰래 보거나, 내 카드 정보를 빼가거나, 악용한다면 인터넷으로 더 이상 안전한 거래를 할 수 없을 것이다. 

HTTP는 암호화되지 않은 방법으로 데이터를 전송하기 때문에 서버와 클라이언트가 주고 받는 메시지를 감청하는 것이 매우 쉽다. 예를 들어, 로그인을 위해서 서버로 비밀번호를 전송하거나, 또는 중요한 기밀 문서를 열람하는 과정에서 악의적인 감청이나 데이터의 변조 등이 일어날 수 있다는 것이다. 이를 막을 수 있도록 만든 것이 HTTPS이다. 

# HTTS란?

> HTTPS는 **HTTP에 Secure라는 말이 추가된 것**이다. 즉, HTTPS는 **보안이 강화된 HTTP**라는 것을 짐작할 수 있다.

인터넷은 안전한 통신을 위하여 암호화라는 것을 한다. 암호화란 일반적인 평문을 알아볼 수 없도록 암호화하여 암호문으로 만드는 과정이다. 암호문을 상대방에게 전달하고, 상대방은 이를 다시 복호화하여 평문으로 열람할 수 있다. 이와 같은 과정을 웹 브라우저와 웹 서버에게 사용하는 대표적인 기술이 바로 **HTTPS(Hypertext Transfer Protocol Secure)**이다. 인터넷 콘텐츠를 전달하는 TCP 프로토콜의 일종인 HTTP에 S(Secure) 기능을 더한 것이다.

# SSL(TLS)이란?

HTTPS는 **SSL(Secure Socket Layer)/TLS(Transport Layer Security) 전송 기술**을 사용한다. 단어의 원어에서 알 수 있듯이, **TCP, UDP와 같은 일반적인 인터넷 통신에 안전한 계층(layer)을 추가하는 방식**이다. 그리고 이 기술을 구현하기 위해 웹 서버에 설치하는 것이 SSL/TLS 인증서이다. TLS는 SSL의 개선 버전으로, 최신 인증서는 TLS를 사용하지만 편의적으로 SSL 인증서라고 쭉 부르고 있다.

# 대칭키, 공개키(=비대칭키)

### 대칭키

대칭키 암호화 방식(symmetric-key algorithm)이란, **'하나의 키(key)'로 평문을 암호화하고, 다시 암호문을 원래의 평문으로 복호화할 때 사용하는 방식**이다. 대문을 잠그는 자물쇠를 떠올려보면, 자물쇠를 잠근 열쇠만이 그 자물쇠를 다시 열 수 있다. 즉, 잠그고 여는 것 모두가 **하나의 열쇠를 사용**한다. 그렇지만 만약 내가 이 열쇠를 잃어버렸고, 누군가 내 집 주소를 아는 사람이 열쇠를 주었다면 어떤 상황이 될까? 이렇듯 대칭키 암호화 방식은 키를 단 하나만 사용하는 간편함이 있지만, 키를 분실하거나 누군가에게 도난을 당한다면 내 암호문을 누군가가 복호화하여 볼 수 있다는 치명적인 단점이 있다.

### 공개키(=비대칭키)

공개키 암호화 방식(Public Key Cryptography)은 **공개키, 개인키 이렇게 두 개의 키를 한 쌍(key pair)으로 각각 암호화/복호화에 사용**한다. 일반적으로 공개키로 암호화한 것을 개인키로 복호화한다. 개인키를 먼저 만들고, 여기서 공개키를 파생하여 한 쌍의 키를 만들기 때문에 key pair라 부르는 것이다. 만약 같은 쌍이 아닌 다른 키를 사용하려 한다면 암호화/복호화가 불가능하다.

공개키는 말 그대로 누구에게나 공개할 수 있는 키이다. 위 그림 예제에도 영희의 공개키는 이미 철수에게 공개되어, 철수가 보유하고 사용할 수 있었습니다. 같은 맥락으로 철수의 공개키는 영희에게 공유되어, 영희가 철수에게 암호문을 전달할 때 사용될 것이다. 따라서 공개키 암호화 방식은 사전에 안전하게 서로의 공개키를 나누어 받는 과정이 필요합니다.

이때 사용하는 대표적인 키 교환 알고리즘으로 RSA(Rivest – Shamir – Adleman)와 디피-할만(Diffie-Hellman) 알고리즘이 있다. 얼핏 들으면 보안에 사용하는 키를 공개적으로 나누어주는 것이 불안해 보일 수 있지만, 앞에서 말한 대로 짝에 해당하는 개인키 없이는 어차피 복호화가 불가능하기 때문에 상관없습니다. 내가 만든 공개키를 누군가 가져갔다 해도(또는 그것으로 만든 암호문을 가지고 있다고 해도) 개인키 없이 복호화가 불가능하기 때문에, 개인키는 아주 안전하게 보관되어야 한다. 공개키 방식은 대칭키 방식에 비해 안전하지만, 계산 과정이 복잡하고 연산 도중 컴퓨터의 자원이 많이 사용하기 때문에, 실제 IT 시스템에서는 공개키 방식과 대칭키 방식을 적절히 혼합하여 사용한다.

### 참고) Private Key와 Public Key는 쌍으로 동시에 생성된다.

- Private Key로 매번 Public Key를 만들어 낼 수 있는 것은 아니다.
- Private Key를 만들 때, Public Key도 한 번에 만들어진다.

# CA로부터 Server가 인증서를 받는 과정 + HTTP 핸드 셰이크 과정 + SSL 핸드 셰이크(handshake) 과정

## 1. 서버 - 인증 서명 요청서(CSR) 생성

> **1) Server에서 Public Key와 Private Key를 쌍으로 생성한다.**

> **2) Server에서 '인증 서명 요청서(CSR)'를 만든다. ⇒ SHA256과 같은 해쉬 알고리즘으로 해쉬 수행**

→ 국가 코드, 도시, 회사명, 부서명, 이메일, 도메인 주소 등이 들어간다.

→ **Server의 Public Key**도 넣는다.

→ `**Hash(Server에_대한_정보)**`를 CA에 전달

## 2. CA - SSL 인증서 발급

> CA에 '**인증 서명 요청서(CSR)(=`Hash(Server에_대한_정보)`**)'을 보내면, CA가 인증 서명 요청서(CSR)에 **CA의 Private Key**를 통해 전자서명(이 때는 '암호화'라고 표현하지 않고, '전자 서명'이라고 표현한다.)를 한 형태인 **SSL 인증서**를 발급한다.

→ `**CA의_Private_key로_암호화(Hash(Server에_대한_정보))`(= SSL 인증서)**를 Server에 다시 전달

## 3. 서버 - SSL 인증서를 받음

> Server는 CA로부터 암호화된 SSL 인증서를 받는다.

## 4. **클라이언트 - SSL Handshake의 첫 번째 단계 : `Client Hello`**

> **1)  Client가 요청을 하게 되면, 가장 먼저 Server에 3-Way Handshake를 통해 연결을 시도한다.**

→ ex) 브라우저 주소창에서 `http://blog.naver.com`을 치면, HTTP는 TCP의 일종이니 TCP 연결을 위한 3-Way Handshake를 먼저 수행한다. 

> **2) 3-Way Handshake를 연결이 완료되면, 클라이언트(브라우저)가 `Client Hello` 단계를 수행**

HTTPS를 사용하는 것을 알게 된 클라이언트(브라우저)는 `Client Hello` 단계에서 밑의 정보를 보낸다. 

- **브라우저가 지원하는 암호화 방식 모음(Cipher Suites)**
- 브라우저가 순간적으로 생성한 **임의의 난수(Random byte)**
- 만약 이전에 SSL 핸드 셰이크가 완료된 상태라면, 그때 생성된 **세션 아이디(Session ID)**
- 브라우저가 사용하는 SSL 혹은 TLS 버전 정보 (SSL Protocol Version)

## 5. **서버 - SSL HandShake의 두 번째 단계 : `Server Hello`**

> **Client가 보내온 `Client Hello` 패킷을 받아, 밑의 정보를 Client에게 응답한다.**

- 브라우저가 지원하는 **암호화 방식(Cipher Suite)** 중에서 하나를 선택해서 Client로 전달
- Server가 자신의 **SSL 인증서**를 Client로 전달

    → SSL 인증서 내부에는 **Server가 발행한 공개키**가 들어있다.
    (개인키는 Server가 소유한다.)

    → SSL 인증서는 CA(Certificate Authority, 인증 기관)의 개인키로 '**서명**'되어 있고, Server가 CA로부터 미리 발급 받아놓은 상태이다. 

- SSL 인증 내부에 기록되어 있는 **`Server에_대한_정보`(=원본 데이터)**
- **서버가 순간적으로 생성한 임의의 난수(숫자)**

## 6. **클라이언트 - SSL HandShake의 세 번째 단계 : `Premaster Secret` 생성**

> **1) 브라우저는 서버의 SSL 인증서가 믿을만한지 확인한다.**

Client(브라우저)가 **SSL 인증서(`CA의_Private_key로_암호화(Hash(Server에_대한_정보))`)**를 CA의 Public Key로 디코딩해본다. 그러면 `**Hash(Server에_대한_정보)**`의 값을 얻을 수 있다. 그리고 `**Server에_대한_정보**`를 **Hash**화 해서 서로 일치하는 지 체크한다. 이를 통해 **SSL 인증서**가 위조 됐는 지 여부를 체크할 수 있다. 

****참고) 브라우저 내부에 CA의 Public Key가 저장되어 있다.**  

> **2) 브라우저는 자신이 생성한 난수와 서버의 난수를 사용하여 premaster secret을 만들어서 Server로 전송한다.**

Client(브라우저)에서, **서버로부터 받은 랜덤값**과 **Client(브라우저)에서 생성한 랜덤값**을 Server의 Public Key로 암호화한다. 이 암호화한 값을 보고 premaster secret이라고 한다. 그리고 이 값을 Server로 전달한다.

→ `**Server의_Public_key로_암호화(Server_랜덤값 + Client_랜덤값)` = `premaster secret` = `세션 키(session key)`**

****참고) 여기서 말하는 세션은, '쿠키와 세션'에서 말하는 세션과는 다른 의미이다.** 

****참고) 세션 종료가 됐을 때 세션 키를 폐기한다고 되어 있는데, 여기서 말하는 세션은 한 번의 통신을 애기하는 것이 아니다. 사이트에 한 번 접속했을 때부터 종료했을 때까지를 하나의 세션으로 볼 수도 있는 것이다. 즉, 사이트에 한 번 접속한 이후부터 종료할 때까지 세션이 종료되면서 세션 키가 폐기되는 것은 아니라는 말이다.** 

## 7. 서버/클라이언트 - SSL HandShake 종료 & HTTPS 통신 시작

브라우저(Client)와 서버(Server)는 SSL handshake가 정상적으로 완료되었고, 이제는 웹상에서 데이터를 세션 키(session key)를 사용하여 암호화/복호화하며 HTTPS 프로토콜을 통해 주고받을 수 있다. HTTPS 통신이 완료되는 시점에서 서로에게 공유된 세션 키(session key)를 폐기한다. 만약 세션(session)이 여전히 유지되고 있다면, 브라우저는 SSL handshake 요청이 아닌 세션 ID만 서버에게 알려주면 된다. `Client Hello` 과정에서 보면 이전에 SSL Handshake가 완료되었을 때, 세션 아이디를 Server한테 보내는 것을 확인할 수 있다. 
(다만, Googling 해보시면 SSL 핸드 셰이크 과정에 대한 내용이 글마다 조금씩 다릅니다. 구현체마다 다양한 옵션을 가지고 있어서 그런 것인데, 원리는 같은 것이니 크게 신경 쓰지 않으셔도 된다)

SSL 인증서 과정에는 대칭키 방식과 공개키 방식 두 개 모두 사용되었다. 앞에서 설명했듯, 모든 웹 콘텐츠의 전달을 공개키 방식으로 한다면 웹 서버와 브라우저에 많은 부담이 되기 때문에 SSL Handshake 단계까지는 공개키 방식, 그 이후의 HTTPS 통신은 대칭키 방식을 사용하는 것이다.

# CA란?

인증서의 역할은 '클라이언트가 접속한 서버'가 '클라이언트가 의도한 서버'가 맞는지를 보장하는 역할을 한다. 이 역할을 하는 민간기업들을 **CA(Certificate authority)**라고 한다. CA는 아무 기업이나 할 수 있는 것이 아니고 신뢰성이 엄격하게 공인된 기업들만이 참여할 수 있다. 

SSL을 통해서 암호화된 통신을 제공하려면 CA를 통해서 인증서를 구입해야 한다. 

**CA의 시장점유율**

[Certificate authority](https://en.wikipedia.org/wiki/Certificate_authority)

(2018년 5월 기준)

- IdenTrust : 39.7%
- Comodo : 34.9%
- DigiCert : 12.3%

# References

### HTTPS, SSL, TSL 관련

[HTTPS를 위한 SSL/TLS 핸드 셰이크 작동원리](https://brunch.co.kr/@sangjinkang/38)

[HTTPS 통신과정 쉽게 이해하기 #3(SSL Handshake, 협상)](https://aws-hyoh.tistory.com/entry/HTTPS-%ED%86%B5%EC%8B%A0%EA%B3%BC%EC%A0%95-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-3SSL-Handshake)

[HTTPS와 SSL 인증서 - 생활코딩](https://opentutorials.org/course/228/4894)

[HTTPS와 SSL 인증서, SSL 동작방법](https://wayhome25.github.io/cs/2018/03/11/ssl-https/)

[Https 통신과정, TCP/IP 핸드 쉐이킹 과정](https://lion-king.tistory.com/entry/Https-SSL-handshake)

[HTTPS를 알아보자](https://velog.io/@logqwerty/HTTPS%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)

[SSL이란 무엇인가](https://study-recording.tistory.com/11)

### 전자 서명 관련

[옷 좋아하는 개발자. : 네이버 블로그](https://blog.naver.com/vjhh0712v/221439905323)

[[스크랩]암호화, 전자서명, 인증서와 SSL](https://epthffh.tistory.com/entry/%EC%95%94%ED%98%B8%ED%99%94-%EC%A0%84%EC%9E%90%EC%84%9C%EB%AA%85-%EC%9D%B8%EC%A6%9D%EC%84%9C%EC%99%80-SSL)

[RSA 인증서 (Certification) 와 전자서명 (Digital Sign)의 원리](https://rsec.kr/?p=426)

[NAVER D2](https://d2.naver.com/helloworld/744920)