# 브라우저에 URL을 입력하면 어떤 일이 벌어질까?

### 목차

1. [개요](#1-개요)
2. [웹브라우저 URL을 입력하고 Enter 키 입력](#2-웹브라우저-url을-입력하고-enter-키-입력)
3. [웹 브라우저가 도메인명의 IP 주소 조회](#3-웹-브라우저가-도메인명의-ip-주소-조회)
4. [웹 브라우저가 서버와의 TCP 연결 시작](#4-웹-브라우저가-서버와의-tcp-연결-시작)
5. [웹 브라우저가 HTTP 요청을 서버로 전송](#5-웹-브라우저가-http-요청을-서버로-전송)
6. [웹 서버가 요청을 처리하고 응답을 다시 전송](#6-웹-서버가-요청을-처리하고-응답을-다시-전송)
7. [웹 브라우저가 콘텐츠 렌더링](#7-웹-브라우저가-콘텐츠-렌더링)
8. [결론](#8-결론)

### 1. 개요

우리는 매일 웹 브라우저를 열고 즐겨찾는 웹사이트를 탐색한다. 매일 방문은 하지만 어떤 일이 벌어지는지 어렴풋이 동작을 생각하는 나를 보고 정리를 한 번 해야겠다고 생각했다.

이 글에서는 웹브라우저에 URL을 입력하면 어떤 일이 벌어지는지 살펴보겠다.

### 2. 웹브라우저 URL을 입력하고 Enter 키 입력

<img width="579" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/ef4b6dfb-dea8-469d-a33c-0fd2b5b71d8d">


위와 같이 브라우저에 [`https://www.naver.com`](https://www.naver.com) 입력한 URL을 분류해보자.

<img width="573" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/9eea2bf9-9409-4eea-ba22-69de0f7c2c0e">

즉 [`naver.com`](http://naver.com) 도메인이 속한 이름이 `www`인 컴퓨터이고 `https://` 프로토콜로 연결을 한다라고 생각하면 된다.

### 3. 웹 브라우저가 도메인명의 IP 주소 조회

1. 브라우저는 웹사이트의 IP 주소를 찾기 위해 DNS 항목에 대한 다음과 같은 캐시를 확인한다. 캐시를 찾지 못하면 다음 단계로 넘어간다.

<img width="571" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/3bdde1ff-50d2-42d3-a409-1fc3e4408128">

- Browser Cache
    
    브라우저는 특정 기간동안 유저가 방문한 웹사이트의 DNS 기록을 보관한다. 따라서 브라우저는 가장 먼저 Browser Cache를 찾아본다.
    
- Operating System Cache
    
    Browser Cache에 DNS 기록이 없다면, 브라우저는 OS에 시스템 콜(system call)을 생성해 DNS 기록을 조회한다.
    
- Router Cache
    
    브라우저와 OS 등 유저의 컴퓨터에 기록이 없다면, 브라우저는 자체 DNS 캐시를 유지관리하는 라우터와 통신한다.
    
- ISP(Internet Service Provider) Cache
    
    유저의 ISP는 DNS 레코드의 캐시를 포함하는 자체 DNS 서버를 유지관리한다.
    
2. 캐시에 없으면 ISP의 DNS 서버는 DNS 쿼리를 시작해 도메인 이름을 호스팅하는 서버의 IP 주소를 찾는다. 요청은 요청의 정보내용과 대상 IP 주소가 포함된 작은 데이터 패킷을 사용해 전송된다.

```
ISP란, Internet Service Provider로 개인이나 기업체에게 인터넷 접속 서비스, 웹사이트 구축 및 웹호스팅 서비스 등을 제공하는 회사를 말한다.
대표적으로 한국에서는 KT, SK브로드밴드, LG U+ 등이 있다.
```

### 4. 웹 브라우저가 서버와의 TCP 연결 시작

브라우저가 IP 주소를 찾으면 브라우저는 서버와 정보를 교환하기 위해 TCP/IP 과정을 거친다. 

<img width="567" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/5cdbe57f-b053-4d0a-9b5a-8098ade685e1">

### 5. 웹 브라우저가 HTTP 요청을 서버로 전송

브라우저는 웹 서버에 HTTP GET 또는 POST 요청을 보낸다.

```
GET /blog/1620 HTTP/1.1
Host: channy.creation.net
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
sec-ch-ua: " Not A;Brand";v="99", "Chromium";v="90", "Google Chrome";v="90"
sec-ch-ua-mobile: ?0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0(Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36(KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: <https://channy.creation.net/>
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
dnt: 1
sec-gpc: 1
```

### 6. 웹 서버가 요청을 처리하고 응답을 다시 전송

호스트 컴퓨터의 서버는 해당 요청을 처리하고 HTTP 응답상태와 함께 JSON, XML, HTML 형태의 응답을 보낸다.

```
HTTP/1.1 200 OK
Date: Tue, 25 May 2021 19:40:59 GMT
Server: Apache
X-Frame-Options: SAMEORIGIN
X-Powered-By: Express
Cache-Control: max-age=0, no-cache
Content-Type: text/html; charset=utf-8
Vary: Accept-Encoding
X-Mod-Pagespeed: 1.13.35.2-0
Content-Encoding: br
Keep-Alive: timeout=1, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked

<!DOCTYPE html>
<html lang="ko">
<head>
    나머지 HTML 항목
```

### 7. 웹 브라우저가 콘텐츠 렌더링

<img width="577" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/06b4531e-b03d-46aa-9ecb-ebb796b6bfc1">

이때 가장 먼저 HTML의 뼈대를 렌더링하고, 이미지와 CSS stylesheets, 자바스크립트 등의 정적 파일에 대해 추가적으로 요청을 보낸다. 이러한 정적파일은 브라우저에 캐싱되어, 다음에 페이지를 방문하더라도 다시 요청을 보내지 않는다.

### 8. 결론

웹 브라우저에서 URL을 입력했을때, 일어나는 일들을 살펴보았다. 우리가 매일 같이 URL을 입력하여 접속하는 것을 정리 함으로써 되새김 되어 좋았고 정리하자면 다음과 같다.

1. 웹 브라우저에 URL을 입력하고 Enter 키를 누릅니다.
2. 웹 브라우저가 도메인의 IP 주소를 조회합니다. (먼저 캐시를 찾고, 그다음 DNS를 검색합니다.)
3. 웹 브라우저가 찾은 IP 주소를 기반으로 서버와의 TCP 연결을 시작합니다.
4. 웹 브라우저가 HTTP 요청을 서버로 전송합니다. (필요한 경우, HTTPS 보안 통신이 진행됩니다.)
5. 웹 서버가 요청을 처리하고 응답을 다시 웹 브라우저로 전송합니다.
6. 웹 브라우저가 전송 받은 콘텐츠를 렌더링합니다.

### 참고

- https://www.youtube.com/watch?v=0cU4sIP-OuE
- https://aws.amazon.com/ko/blogs/korea/what-happens-when-you-type-a-url-into-your-browser/