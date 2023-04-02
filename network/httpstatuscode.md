# HTTP 상태 코드 소개

### 목차

1. [HTTP 상태코드](#1-http-상태코드)
2. [1xx(Informational)](#2-1xxinformational)
3. [2xx(Successful)](#3-2xxsuccessful)

    3-1. [200 (OK)](#1-200-ok)
    
    3-2. [201 (Created)](#2-201-created)
    
    3-3. [202 (Accepted)](#3-202-accepted)
    
    3-4. [204 (No Content)](#4-204-no-content)
    
4. [3xx(Redirection)](#4-3xxredirection)
    
    4-1. [리다이렉션 이해](#1-리다이렉션-이해)
    
    4-2. [영구 리다이렉션](#2-영구-리다이렉션)
    
    4-2-1. [301 (Moved Permanently)](#2-1-301-moved-permanently)
    
    4-2-2. [308 (Permanent Redirect)](#2-2-308-permanent-redirect)
    
    4-3. [일시 리다이렉션](#3-일시적인-리다이렉션)
    
    4-3-1. [302 (Found)](#3-1-302-found)
    
    4-3-2. [307 (Temporary Redirect)](#3-2-307-temporary-redirect)
    
    4-3-3. [303 (See Other)](#3-3-303-see-other)
    
    4-4. [PRG : Post/Redirect/Get](#4-prg--postredirectget)
    
5. [4xx(Client Error)](#5-4xxclient-error)
    
    4-1. [400 (Bad request)](#1-400-bad-request)
    
    4-2. [401 (Unauthorized)](#2-401-unauthorized)
    
    4-3. [403 (Forbidden)](#3-403-forbidden)
    
    4-4. [404 (Not Found)](#4-404-not-found)
    
6. [5xx(Server Error)](#6-5xxserver-error)
    
    5-1. [500 (Internal Server Error)](#1-500-internal-server-error)
    
    5-2. [503 (Service Unavailable)](#2-503-service-unavailable)
    

## 1. HTTP 상태코드

클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능.

응답은 5개의 그룹으로 나누어지며 아래와 같다.

- **1xx(Informational): 요청이 수신되어 처리중**
- **2xx(Successful):요청 정상 처리**
- **3xx(Redirection):요청을 완료하려면 추가 행동이 필요**
- **4xx(Client Error): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행할 수 없음.**
- **5xx(Server Error):서버 오류, 서버가 정상 요청을 처리하지 못함**

예를 들어, 모르는 상태코드가 나올 경우, 299는 2xx(Successful)대 상태코드구나 451인 경우 4xx(Client Error) 처럼 상위 상태코드로 해석해서 처리하면 된다.

## 2. 1xx(Informational)

- 요청이 수신되어 처리중
- 잘 사용되는 않는 상태코드라 가볍게 넘어가도 좋다

| 상태 코드 | 상태 메세지 | 설명 |
| --- | --- | --- |
| 100 | Continue | 처리가 되었으니 다음으로 진행 |
| 101 | Switching Protocols | 서버가 프로토콜을 전환중 |
| 102 | Processing | 서버가 요청을 아직 처리중이라 제대로된 응답을 알려줄수 없음 |
| 103 | Early Hints | 웹페이지에 필요한 리소스에 대한 힌트를 제공하여 리소스를 사전 로드하여 로딩을 빠르게 |

## 3. 2xx(Successful)

- 요청 정상 처리

### 1. 200 (OK)

요청성공

<img width="714" alt="image" src="https://user-images.githubusercontent.com/59176149/229347631-eb7b6e7d-05d6-47b0-98d3-00f665b4c5d4.png">

- 클라이언트가 요청을 하면 서버에서 결과를 정상적으로 잘 처리해서 응답하면 200을 준다.

### 2. 201 (Created)

요청 성공해서 새로운 리소스가 생성됨

<img width="720" alt="image" src="https://user-images.githubusercontent.com/59176149/229347644-8032fecc-d52d-4ee3-ad9d-58b28c400cb6.png">

- 위와 같이 클라이언트가 요청을 하면 서버에서는 HTTP 헤더에 Location이란 곳에 새로 생성된 리소스의 URI를 넣어주고 응답한다.

### 3. 202 (Accepted)

요청이 접수되었으나 처리가 완료되지 않았음

- 배치 처리 같은 곳에서 사용
- ex) 요청 접수후 1시간 뒤에 배치 프로세스가 요청을 처리함

### 4. 204 (No Content)

서버가 요청을 성공적으로 수행했지만, 응답 페이로드 본문에 보낼 데이터가 없음

- ex) 웹 문서 편집기에서 save 버튼
- save 버튼의 결과로 아무 내용이 없어도 된다.
- save 버튼을 눌러도 같은 화면을 유지해야 한다.
- 결과 내용이 없어도 204 메시지(2xx)만으로 성공을 인식할 수 있다.

| 상태 코드 | 상태 메세지 | 설명 |
| --- | --- | --- |
| 200 | OK | 클라이언트의 요청을 서버가 정상적으로 처리 |
| 201 | Created | 클라이언트의 요청을 서버가 정상적으로 처리했고 새로운 리소스가 생김 |
| 202 | Accepted | 클라이언트의 요청은 정상적이나, 서버가 아직 처리를 완료하지 못해서 일단 알았다는 표시 (요청이 크고 무거운 경우) |
| 203 | Non-Authoritative Information | 웹사이트가 프록시 서버(CDN 또는 VPN 또는 기타)를 사용할 때 반환되는 상태 코드 |
| 204 | No Content | 클라이언트의 요청은 정상적이다. 하지만 제공할 컨텐츠가 없다. |
| 205 | Reset Content | 브라우저를 새로 고침하라는 의미 |
| 206 | Partial Content | 리소스 범위의 일부 부분만 반환 |
| 207 | Multi-Status | 응답 바디가 여러개 혼합되어 응답됨- WebDAV에 사용되는 상태코드 |
| 208 | Already Reported | 이미 앞에서 열거되었음을 의미- WebDAV에 사용되는 상태코드 |
| 218 | This is fine | 오류가 발생했지만 여긴(apache 서버) 괜찮아~ 의미아파치(Apache) 웹 서버 에서 사용되는 비공식 HTTP 응답 코드 |
| 226 | IM Used | 서버가 GET 요청에 대한 응답 의무를 다했다는 의미HTTP Delta Encoding 기법을 이용한 부분 수정 리소스만 반환하여 네트워크 다운로드를 아낌 |

## 4. 3xx(Redirection)

- 요청을 완료하기 위해 유저 에이전트의 추가 조치 필요

### 1. 리다이렉션 이해

- 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location위치로 자동 이동(리다이렉트)
- 예를 들어 /event 페이지를 사용했다가 더 이상 /event 페이지를 쓰지 않고 새로운 /new-event를 쓰기로 했다고 해보자.
- 기존 사용자들은 북마크를 해두었거나 기존 이벤트 페이지 여러군데에서 공유가 되었을 수도 있을 것이다.

<img width="709" alt="image" src="https://user-images.githubusercontent.com/59176149/229347675-09cd6bae-e725-4e67-971a-b93eedf61474.png">

1. 요청
    - 클라이언트가 URL로 /event를 치고 들어 온다고 하자
2. 응답
    - 서버는 /event 경로가 /new-event로 영구적으로 이동했다고 클라이언트에게 응답해줍니다.
3. 자동리다이렉트
    - 클라이언트가 3xx 상태코드고 Location 헤더 필드가 있으면 URL창에 /new-event로 변경하고 이동을 시켜준다.
4. 요청
    - 클라이언트가 바뀐 /new-event 경로로 서버에 다시 요청을 한다.
5. 응답
    - 그러고 서버는 /new-event에 대한 응답 코드를 클라이언트에게 다시 보내준다.

### 2. 영구 리다이렉션

- 특정 리소스의 URI가 영구적으로 이동
- 원래의 URL를 사용X, 검색 엔진 등에서도 변경 인지

### 2-1. 301 (Moved Permanently)

- 리다이렉트시 요청 메서드가 GET으로 변하고, 본문이 제거 될 수 있음

<img width="714" alt="image" src="https://user-images.githubusercontent.com/59176149/229347683-dc7fb963-5e15-4bd5-b333-85fbbea630bb.png">

### 2-2. 308 (Permanent Redirect)

- 301과 기능은 같음
- 리다이렉트시 요청 메서드와 본문 유지(처음 POST를 보내면 리다이렉트도 POST 유지)

<img width="735" alt="image" src="https://user-images.githubusercontent.com/59176149/229347697-afd6781b-a5c1-478e-b04c-b96c858fee65.png">

### 3. 일시적인 리다이렉션

- 리소스의 URI가 일시적으로 변경
- 따라서, 검색 엔진 등에서 URL을 변경하면 안됨

### 3-1. 302 (Found)

- 리다이렉트시 요청 메서드가 Get으로 변하고, 본문이 제거 될 수 있음

### 3-2. 307 (Temporary Redirect)

- 302와 기능은 같음
- 리다이렉트시 요청 메서드와 본문 유지(요청 메서드를 변경하면 안된다.)

### 3-3. 303 (See Other)

- 302와 기능은 같음
- 리다이렉트시 요청 메서드가 GET으로 변경

### 4. PRG : Post/Redirect/Get

- POST로 주문후에 웹 브라우저를 새로고침하면?
- PRG를 사용하기전에는 아래와 같이 새로고침을 하게 되면 다시 주문 데이터를 요청하며 중복 주문이 될 수 있다.

<img width="705" alt="image" src="https://user-images.githubusercontent.com/59176149/229347745-6982976b-0ae9-47bf-b0dd-46c589d0f53b.png">


- PRG 이후 리다이렉트시, URL이 이미 POST → GET으로 리다이렉트 되면서 새로고침을 해도 GET으로 결과 화면만 조회하는 것을 볼 수 있다.

<img width="713" alt="image" src="https://user-images.githubusercontent.com/59176149/229347751-31bbc563-1360-4677-a2b8-28fcda36b321.png">

- PRG를 적용함으로써, POST로 주문 후에 새로고침으로 인한 중복 주문 방지
- POST로 주문 후에 주문 결과 화면을 GET메서드로 리다이렉트
- 새로고침해도 결과 화면을 GET으로 조회
- 중복 주문 대신에 결과 화면만 GET으로 다시 요청

사용자 입장에서도 실수로 새로고침을 하더라도 중복 주문을 방지할 수 있고 서버에서도 오류가 줄어들 수 있는 효과를 누릴 수 있다. 

| 상태 코드 | 상태 메세지 | 설명 |
| --- | --- | --- |
| 300 | Multiple Choices | 요청에 대해서 둘 이상의 가능한 응답이 있음을 나타낸다.- 실무에서 거의 사용하지 않음 |
| 301 | Moved Permanently | 영구적으로 이동 - 영구 리다이렉션- 메서드가 GET으로 바뀜 |
| 302 | Found | 다른 URL에서 리소스를 찾음- 일시 리다이렉션- 메서드가 GET으로 바뀜 |
| 303 | See Other | 다른 URL에서 리소스를 찾음- 일시 리다이렉션- 메서드가 GET으로 바뀜 |
| 304 | Not Modified | 리소스 복사본 상태가 수정 되지 않아 최신 상태이므로 캐시를 이용- 특수 리다이렉션 |
| 305 | Use Proxy | 리소스가 프록시를 통해서만 액세스될 수 있음을 표현- 보안 문제로 더이상 사용되지 않음 |
| 306 | Switch Proxy / Undefined | 클라이언트가 대체 프록시를 사용하도록 리다이렉션(switch) 시킨다- 보안 문제로 더이상 사용되지 않음 |
| 307 | Temporary Redirect | 임시로 이동- 일시 리다이렉션- 메서드가 유지됨 |
| 308 | Permanent Redirect | 영구 이동- 영구 리다이렉션- 메서드가 유지됨 |

## 5. 4xx(Client Error)

- 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행
- 오류의 원인이 클라이언트에 있다.

### 1. 400 (Bad request)

클라이언트가 잘못된 요청을 해서 서버가 요청을 처리할 수 없음

- 요청 구문, 메시지 등등 오류
- 클라이언트는 요청 내용을 다시 검토하고, 보내야함
- ex) 요청 파라미터가 잘못되거나, API 스펙이 맞지 않을 때

### 2. 401 (Unauthorized)

클라이언트가 해당 리소스에 대한 인증이 필요함.

- 인증 되지 않음
- 401 오류 발생시 응답에  WWW-Authenticate 헤더와 함께 인증 방법을 설명
- 참고
    - 인증(Authentication): 본인이 누구인지 확인, (로그인)
    - 인가(Authorization): 권한부여 (ADMIN 권한처럼 특정 리소스에 접근할 수 있는 권한, 인증이 있어야 인가가 있음)
    - 오류 메시지가 Unauthorized 이지만 인증 되지 않음(이름이 아쉬움)

### 3. 403 (Forbidden)

서버가 요청을 이해했지만 승인을 거부함

- 주로 인증 자격 증명은 있지만, 접근 권한이 불충분한 경우
- ex) 어드민 등급이 아닌 사용자가 로그인은 했지만, 어드민 등급의 리소스에 접근하는 경우

### 4. 404 (Not Found)

- 요청 리소스가 서버에 없음
- 또는 클라이언트가 권한이 부족한 리소스에 접근 할때 해당 리소스를 숨기고 싶을때

| 상태 코드 | 상태 메세지 | 설명 |
| --- | --- | --- |
| 400 | Bad Request | 클라이언트가 잘못된 요청을 보냄을 의미주로 요청 구문, 메시지 등의 문법 오류로 인한 문제가 해당 |
| 401 | Unauthorized | 요청자는 인증(authentication) 되지 않아 수행할 수 없음을 표현참고로 401 응답은 Unauthorized 가 아닌 Unauthenticated 가 알맞는 단어이다. |
| 402 | Payment Required | 나중에 사용될 것을 대비해 예약된 비표준 응답 코드 |
| 403 | Forbidden | 요청자는 승인(autorization)되지 않아 작업을 진행할수 없음인증 자격(로그인)은 증명되었으나, 회원 등급에 의해 접근 권한이 불충분할때 사용 |
| 404 | Not Found | 클라이언트가 요청한 자원이 존재하지 않음 |
| 405 | Method Not Allowed | 요청이 허용되지 않은 메소드임을 의미서버에서 해당 요청 HTTP 메소드에 대해 기능을 제한/금지 함 |
| 406 | Not Acceptable | 콘텐츠 협상에 일치하는 것이 없음현업에서는 이 오류 코드를 거의 사용하지 않는다. |
| 407 | Proxy Authentication Required | 프록시 인증을 요구401 Unauthorized 와 같으나, 프록시 버전이라고 보면 된다. |
| 408 | Request Timeout | 요청이 너무 커 처리 시간이 초과되어 서버에서 요청을 처리하지 아니하고 연결을 닫음 |
| 409 | Conflict | 크라이언트의 요청이 서버의 상태와 충돌이 발생요청 처리 중 비지니스 로직상 불가능하거나 모순이 생긴 경우 사용된다. |
| 410 | Gone | 리소스가 영구히 삭제됨404 Not Found와 비슷하나 410 응답은 요청한 컨텐츠가 서버에서 영구히 삭제되어 전달해 줄 주소가 존재하지 않을때 사용된다. |
| 411 | Length Required | 요청 메시지에 Content-Length 헤더가 있을 것을 요구 |
| 412 | Precondition Failed | 클라이언트의 조건부 요청 실패클라이언트가 캐시에 대한 조건부 요청을 했는데 실패했을 때 응답된다. (Etag나 수정일짜 불일치)- 304 응답은 GET 과 HEAD 메소드에만 동작한다. - 412 응답은 이를 제외한 POST, PUT, PATCH 등 메소드에만 동작한다. |
| 413 | Payload Too LargeRequest Entity Too Large | 요청 본문이 서버에서 정의한 한계보다 너무 커 처리할수 없음 |
| 414 | URI Too LongRequest URI Too Long | 요청 URI이 너무 길어 처리할수 없음 |
| 415 | Unsupported Media Type | 요청한 미디어 포맷은 서버에서 지원하지 않음 |
| 416 | Range Not Satisfiable | Range 헤더 필드에 요청한 지정 범위를 만족시킬 수 없음 |
| 417 | Expectation Failed | Expect 요청 헤더 필드로 요청한 예상 반환 코드를 만족 시킬 수 없음 |
| 418 | I’m a teapot | 만우절 농담 상태 코드 |
| 420 | Method Failure or Enhance your calm | 클라이언트 오류를 ​​나타내기 위해 서버에서 반환하는 비공식 클라이언트 오류 Spring Framework에서 메서드가 실패했음을 나타내는 비공식 응답코드로도 쓰였음 (지금은 안쓰임) |
| 421 | Misdirected Request | 의도하지 않은 요청을 받아 서버가 응답을 생성할 수 없음을 나타냄 |
| 422 | Unprocessable Entity | 이 응답은 서버가 요청을 이해하고 요청 문법도 올바르지만 요청된 지시를 따를 수 없음을 나타냄 |
| 423 | Locked | 요청에 대한 대상 파일 또는 폴더가 잠겨 있을때 반환- WebDAV에 사용되는 상태코드 |
| 424 | Failed Dependency | 이전 요청이 실패하였기 때문에 지금의 요청도 실패하였음을 의미- WebDAV에 사용되는 상태코드 |
| 426 | Upgrade Required | HTTP 프로토콜 업그레이드 권고 |
| 428 | Precondition Required | 조건부 요청이 요구됨서버가 클라이언트에게 요청을 조건부로 해야 함을 나타낸다. |
| 429 | Too Many Requests | 클라이언트가 일정 시간 동안 너무 많은 요청을 보낸 경우 |
| 431 | Request Header Fields Too Large | 헤더 필드가 너무 커서 요청을 처리하지 않음 |
| 451 | Unavailable For Legal Reasons | 법적인 이유로 비허용됨 |

## 6. 5xx(Server Error)

- 서버 오류, 서버가 정상 요청을 처리하지 못함

### 1. 500 (Internal Server Error)

- 서버 내부 문제로 오류 발생
- 애매하면 500 오류

### 2. 503 (Service Unavailable)

- 서버가 일시적은 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음
- Retry-After 헤더 필드로 얼마뒤에 복구 되는지 보낼 수도 있음.

| 상태 코드 | 상태 메세지 | 설명 |
| --- | --- | --- |
| 500 | Internal Server Error | 서버 내부 문제 발생서버 사용량의 폭주로 인해 서비스가 일시적으로 중단되거나, 백엔드 스크립트의 오류 등 원인은 다양하다. 웹 서버에 문제가 있음을 의미하지만, 정확한 문제에 대해 더 구체적으로 설명할 수 없을때 내뱉는 응답 코드이다. |
| 501 | Not Implemented | 요청에 대해 구현되지 않아 수행하지 아니함다만 앞으로 영원히 기능을 지원하지 않는 다는 의미보다는, 추후에 기능이 개발되면 지원한다는 의미가 더 크다. |
| 502 | Bad Gateway | 게이트웨이가 잘못되어, 서버가 잘못된 응답을 수신함을 의미보통은 접속이 폭주하는 등의 원인으로 서버에서 어떤 이유로 통신장애가 발생하였을 경우에 발생 |
| 503 | Service Unavailable | 서비스 이용 불가 (일시적) |
| 504 | Gateway Timeout | 게이트웨이 시간 초과로, 서버에서 요청을 처리하지 아니하고 연결을 닫음408 Request Timeout 과 비슷해 보이지만, 다른 서버에게 요청을 보내고 응답을 기다리다 타임아웃이 발생한 게이트웨이나 프록시 서버에서 온 응답이라는 점이 다르다. |
| 505 | HTTP Version Not Supported | 서버에서 지원되지 않는 HTTP 버전이라 처리 불가 |
| 506 | Variant Also Negotiates | 실험적인 프로토콜이며 공식적으로 표준으로 채택하지 않은 응답 코드 |
| 507 | Insufficient Storage | 스토리지 공간 부족- WebDAV에 사용되는 상태코드 |
| 508 | Loop Detected | 무한 루프를 감지- WebDAV에 사용되는 상태코드 |
| 510 | Not Extended | 실험적인 프로토콜이며 공식적으로 표준으로 채택하지 않은 응답 코드 |
| 511 | Network Authentication Required | 네트워크 인증 요구ex) 공공 와이파이 인증 |
| 599 | Network Connect Timeout Error | 네트워크 연결 시간 초과 오류일부 프록시에서 사용하는 비공식 HTTP 상태 코드 |

### 참고

- [https://www.inflearn.com/course/http-웹-네트워크/dashboard](https://www.inflearn.com/course/http-웹-네트워크/dashboard)
- [https://developer.mozilla.org/ko/docs/Web/HTTP/Status](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)
- [https://ko.wikipedia.org/wiki/HTTP_상태_코드](https://ko.wikipedia.org/wiki/HTTP_%EC%83%81%ED%83%9C_%EC%BD%94%EB%93%9C)
- [https://inpa.tistory.com/entry/HTTP-🌐-상태-코드-1XX-5XX-총정리판-📖](https://inpa.tistory.com/entry/HTTP-%F0%9F%8C%90-%EC%83%81%ED%83%9C-%EC%BD%94%EB%93%9C-1XX-5XX-%EC%B4%9D%EC%A0%95%EB%A6%AC%ED%8C%90-%F0%9F%93%96)
