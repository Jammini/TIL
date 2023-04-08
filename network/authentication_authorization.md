# 인증(Authentication)과 인가(Authorization)

### 목차

1. [인증과 인가](#1-인증과-인가)
    
    1-1. [인증(Authentication)](#1-인증authentication)
    
    1-2. [인가(Authorization)](#2-인가authorization)
    
2. [인증의 방법](#2-인증의-방법)
    
    2-1. [Request Header 활용](#1-request-header-활용)
    
    2-1-1. [문제점](#1-1-문제점)
    
    2-2. [Browser(Cookie) 활용](#2-browsercookie-활용)
    
    2-2-1. [문제점](#2-1-문제점)
    
    2-3. [Server (Session) 활용](#3-server-session-활용)
    
    2-3-1. [문제점](#3-1-문제점)
    
    2-4. [Token(JWT) 활용](#4-tokenjwt-활용)
    
    2-4-1. [문제점](#4-1-문제점)
    
3. [결론](#3-결론)

## 1. 인증과 인가

- 일반적으로 인증을 진행한 후에 인가가 진행되는게 통상적이라 흔히 인가보다 인증이 먼저 선행되어야 한다.
- 물론, 인증과 인가는 별개의 목적을 가져 인증없이 권한을 부여할 수도 있고, 권한 부여 없이 인증만 진행할 수도 있다.

### 1. 인증(Authentication)

- 식별가능한 정보로 서비스에 등록된 유저의 신원을 증명하는 것.
- 로그인 같이 등록된 사용자인지 여부를 확인하는 것으로 보면 된다.

![image](https://user-images.githubusercontent.com/59176149/230727015-f88d967c-13b6-4864-b439-5488d703c528.png)


### 2. 인가(Authorization)

- 특정 자원을 접근 권한 확인하는 것.
- 사용자가 수행하는 행위에 권한이 있는지 확인하는 절차이다.

![image](https://user-images.githubusercontent.com/59176149/230727028-a8930c1a-1e45-4cbf-8f40-281cec2bc950.png)

## 2. 인증의 방법

1. 인증하기 - Request Header
2. 인증 유지하기 - Browser
3. 안전하게 인증하기 - Server
4. 효율적으로 인증하기 - Token
5. 다른 채널을 통해 인증하기 - OAuth

### 1. Request Header 활용

![image](https://user-images.githubusercontent.com/59176149/230727061-c9edd81a-619e-46b5-b582-8c72030deb08.png)

- 가장 기본적이고 단순한 형태의 인증 방식으로 사용자 ID와 password를 HTTP Request Header에 Base64 Encoding 형태로 넣어서 인증을 요청하는 방식이다.
- 이 인코딩은 브라우저가 처리하는데 아래와 같이 처리된다.

![image](https://user-images.githubusercontent.com/59176149/230727088-48d78af9-8158-425f-a467-5604126ee5b0.png)

- URL에서 ID와 PW인 `user:1q2w3e!`를 파싱한 후에 인코더를 통해서 인코더한 문자인 `dG9uZXk6cGFzc3dvcmQ` 를 요청 헤더에 넣어서 서버로 보낸다.

![image](https://user-images.githubusercontent.com/59176149/230727108-9822069a-19e2-47d5-bf1e-11b54ba52ba1.png)

- 클라이언트의 브라우저가 Base64를 통해 인코딩형태로 Request Header에 넣어 요청을 하면, 서버에서는 이를 디코딩해서 DB에 접근하여 확인하고 OK를 보내는 형식으로 진행이 된다.

### 1-1. 문제점

- 새로운 Request를 할 때마다 매번 인증을 해야한다.

### 2. Browser(Cookie) 활용

- 앞서 본 Request Header로 인증하게 되면 매번 인증을 해야하는 문제점을 보완한게 바로 쿠키이다.
- 쿠키를 이용해 브라우저 내에 로컬 스토리지에 인증(Authentication) 정보를 담아둔다.

![image](https://user-images.githubusercontent.com/59176149/230727151-7a659998-ea91-4527-abcb-446d5cdda479.png)

### 2-1. 문제점

- 쿠키에 사용자 정보가 그대로 노출되어 있으니 해커들에게 쉽게 정보를 노출시키는 것이다.
- 클라이언트가 서버보다 상대적으로 보안에 취약하다는 단점이 존재한다.

### 3. Server (Session) 활용

- 서버에 비해 상대적으로 보안적인 부분에서 취약한 클라이언트를 보완하기 위해 Session을 활용하여 서버의 도움을 받는다.
- 쿠키를 기반으로 하지만, 유저 정보를 로컬 스토리지와 브라우저에 저장하는 쿠키와 달리, 세션은 서버에서 관리한다. 따라서 보안적인 측면에서 일반적인 쿠키보다 우수하다.
- 서버는 클라이언트들을 식별하기 위해 세션 ID를 각각 부여하며 웹 브라우저가 서버에 접속해서 종료할 때까지 인증상태를 유지한다.
- 접속시간에 제한을 두어 일정 시간동안 응답이 없을 시, 정보가 삭제되도록 설정이 가능하다.
- 클라이언트가 요청을 보내면, 해당 서버가 클라이언트에게 식별 ID를 부여하는데 이를 세션ID라고 한다.

![image](https://user-images.githubusercontent.com/59176149/230727172-8df72dc2-a52b-4aac-95f8-4a431e937ddf.png)

- 세션의 동작 방식
1. 클라이언트가 서버 요청시 세션 ID를 부여받는다.
2.  해당 세션 ID에 대해 클라이언트는 쿠키로 저장하여 보관하고 있는다.`Set-Cookie: SESSIONID==aFs28X1ka1d`
3. 클라이언트의 요청 시, 쿠키에 저장된 세션 ID를 같이 전달하여 요청한다.
4. 서버는 쿠키에 저장된 세션 ID를 전달 받아 해당 ID를 통해 클라이언트 정보를 가져와서 사용하여 요청을 처리후, 클라이언트에게 응답한다.

### 3-1. 문제점

- 여러 서버를 지닌 웹 서비스의 경우, 세션 ID 관리면에서 아래와 같은 문제가 생긴다. 서버3에서 Session을 관리하고 있었는데 서버2로 요청을 전달하게 되었을 경우 문제가 생기게 된다.
- 각각의 서버에서 세션을 관리하고 있어서 생긴 문제인 것이다.

![image](https://user-images.githubusercontent.com/59176149/230727219-7adb6ba0-94dd-462a-a079-5235dd3fd073.png)

- 위 문제점을 피하기 위해, 세션ID는 각각의 서버에서 개별적으로 관리하는 정보이기에 세션만을 관리하는 세션 스토리지 서버가 따로 필요하다.

![image](https://user-images.githubusercontent.com/59176149/230727231-3e26ea24-93f2-4618-a252-e418ef464dc2.png)

- 유저 정보를 클라이언트가 아닌 서버에서 관리하기에 보안 면에서 뛰어나지만, 사용자가 많아지면 서버 메모리를 많이 차지하여 서버 과부하와 성능 저하의 원인이 될 수 있다.
- 유저가 너무 많을 경우, 세션 스토리지가 감당을 못하고 터질 수도 있다. 이는 모든 정보를 세션을 활용하여 서버에 저장하는 것이 아닌, 일부 가벼운 정보의 경우 쿠키를 활용하게 되는 이유가 된다.

![image](https://user-images.githubusercontent.com/59176149/230727248-16143eba-8cb8-4498-936a-2524e2b7e7c1.png)

### 4. Token(JWT) 활용

- 토큰 기반 시스템의 존재이유는 바로 서버의 무상태성(Stateless)를 유지하기 위함이다. 상태정보를 저장하지 않으면, 서버는 클라이언트에서 들어오는 요청만으로 작업을 처리하기에 클라이언트와 서버의 연결고리가 없다. 이는 서버의 **확장성(Scalability)**를 높이는 효과를 가져온다.

![image](https://user-images.githubusercontent.com/59176149/230727283-ca21f4e8-9f79-4d14-bd75-e0229f01bf28.png)

- 유저가 ID와 PW로 로그인을 하고 서버측에서 유저정보를 검증한다.
- 검증된 유저라면 서버에서는 secret-key를 이용해 Access Token을 생성한다.
- 생성한 토큰을 받은 클라이언트는 다음 요청부터 토큰을 이용해 서버에 요청한다.

![image](https://user-images.githubusercontent.com/59176149/230727298-7fc8c0c0-072c-4ace-bac3-cd86eafd23a0.png)

- 기존에 세션을 사용했을때는 각각의 서버가 세션로컬리지와의 연관성이 있었지만 각각의 서버가 자신이 가진 secret-key를 이용해 인증을 진행하면 된다.
- 사용자 정보를 클라이언트나 서버나 세션DB에 저장하지 않기 때문에 쿠키의 취약점으로 인해 발생하는 보안적인 문제가 사라진다.

### 4-1. 문제점

- Access Token이 탈취당하면 문제가 생긴다
    - 이를 막기 위해 토큰에 만료기한을 정하고 Refresh Token을 이용해 만료기한이 지나면 토큰을 갱신해준다.
- 결국 토큰도 탈취당할 수 있으므로 토큰도 꾸준히 관리하여야한다.

## 3. 결론
- 전체적인 흐름을 알아보기 위해 정리내용의 부족한 부분이 많이 존재한다
- 보안 관련 부분, JWT, OAuth 등등 더 내용 정리가 된다면 링크를 남기도록 한다.
- OAuth의 개념과 동작방식을 알아보려면 해당링크([OAuth2.0 개념과 동작에 대해 알아보자](https://github.com/Jammini/TIL/blob/master/network/OAuth2.md))로 넘어가서 살펴보자.

### 참고

- [https://www.youtube.com/watch?v=y0xMXlOAfss](https://www.youtube.com/watch?v=y0xMXlOAfss)
- [https://dev.gmarket.com/45](https://dev.gmarket.com/45)
- [https://velog.io/@dev-joon/인증Authentication과-인가Authorization](https://velog.io/@dev-joon/%EC%9D%B8%EC%A6%9DAuthentication%EA%B3%BC-%EC%9D%B8%EA%B0%80Authorization)
