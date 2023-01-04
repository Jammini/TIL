# 모든 개발자를 위한 HTTP 웹 기본 지식

****[모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)**** 내용을 정리 하였습니다.

### 목차
- [인터넷 네트워크](#인터넷-네트워크)
   1. [인터넷 통신](#1-인터넷-통신)
   2. [IP(Internet Protocol)](#2-ipinternet-protocol)
   3. [TCP, UDP](#3-tcp-udp)
   4. [PORT](#4-port---같은-ip-내에서-프로세스-구분)
   5. [DNS](#5-dns)
- [URI와 웹 브라우저 요청 흐름](#uri와-웹-브라우저-요청-흐름)
   1. [URI(Uniform Resource Identifier)](#1-uriuniform-resource-identifier)
   2. [웹 브라우저 요청 흐름](#2-웹-브라우저-요청-흐름)
- [HTTP 기본](#http-기본)
   1. [모든 것이 HTTP](#1-모든-것이-http)
- [HTTP 메서드](#http-메서드)
- [HTTP 메서드의 속성](#http-메서드)
- [HTTP 메서드 활용](#http-메서드-활용)
- [HTTP API 설계 예시](#http-api-설계-예시)
- [HTTP 상태코드 소개](#http-상태코드-소개)
- [HTTP 헤더 - 일반헤더](#http-헤더---일반헤더)


# [인터넷 네트워크]

1. 인터넷 통신
2. IP(Internet Protocol)
3. TCP, UDP
4. PORT
5. DNS

## 1. 인터넷 통신

![Untitled](https://user-images.githubusercontent.com/59176149/210565568-ccc114ce-8c47-43ce-b33d-515b1ba12b19.png)


전송하려는 곳에는 수많은 노드들이 존재한다. 얘네들은 어떤 규칙에 의해서 전달받을까? 

## 2. IP(Internet Protocol)

- IP 패킷정보
    
    ![Untitled 1](https://user-images.githubusercontent.com/59176149/210565687-0dfbd6ca-935e-4431-a565-7038554f49c5.png)
    
- 클라이언트 패킷 전달
    
    ![Untitled 2](https://user-images.githubusercontent.com/59176149/210565693-bfe26651-b082-4e3e-9bab-bf7f07fc63a7.png)

- 서버 패킷 전달
    
    ![Untitled 3](https://user-images.githubusercontent.com/59176149/210565695-c7c18ee9-419a-462a-be85-20ecd0489603.png)
    
- IP 프로토콜의 한계
    1. 비연결성
        1. 패킷을 받을 대상이 없거나 서비스 불능 상태여도 패킷 전송 → ex) PC가 꺼져있을때, 클라이언트가 전달 값을 보냈는데 서버측에서 받을 수 있는 상태인지 확인을 할수가 없음.
    2. 비신뢰성
        1. 중간에 패킷이 사라지면? → ex) 갑작스럽게 전달하는 노드중 하나가 제대로 작동하지 않을 경우.
        2. 패킷이 순서대로 안오면? → ex) 패킷이 중간에 다른 노드를 타다가 순서가 보장이 안될 경우.
    3. 프로그램 구분
        1. 같은 IP를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상이면?

## 3. TCP, UDP

![Untitled 4](https://user-images.githubusercontent.com/59176149/210565699-9b6613b5-ec32-41cb-819c-8fa19836674b.png)

- 프로토콜 계층
    
    ![Untitled 5](https://user-images.githubusercontent.com/59176149/210565701-29ed15d9-fc1a-4690-9f47-5334798a8d2b.png)

- TCP/IP 패킷정보
    
    ![Untitled 6](https://user-images.githubusercontent.com/59176149/210565705-a947df55-a5c5-4a7b-a11f-deeb8780ac94.png)
    
- TCP 특징(Transmission Control Protocol)
    1. 연결지향 - TCP 3 way handshake (가상 연결) → 연결이 되었는지 확인이 가능함. / **비연결성 극복**
        
        ![Untitled 7](https://user-images.githubusercontent.com/59176149/210565708-58da8927-6b0c-4961-908b-9d18df4cb013.png)
        
    2. 데이터 전달 보증 → 메시지를 보냈는데 만약 패킷이 중간에 누락이 되면 메세지를 저기서 못받았다는걸 알수 가 있음. / **비신뢰성 극복**
        
        ![Untitled 8](https://user-images.githubusercontent.com/59176149/210565711-8779723a-48df-4012-985e-0b4dc0c0bf86.png)
        
    3. 순서보장 / **비신뢰성 극복**
        
        ![Untitled 9](https://user-images.githubusercontent.com/59176149/210565714-de85da7d-0836-41ec-ac99-2b20a63111d1.png)
        
    4. 신뢰할 수 있는 프로토콜
    5. 현재는 대부분 TCP 사용
- UDP 특징 - 사용자 데이터그램 프로토콜(User Datagram Protocol)
    1. 하얀 도화지에 비유(기능이 거의 없음)
    2. 연결지향 - TCP 3 way handshake X
    3. 데이터 전달 보증 X
    4. 순서 보장 X
    5. 데이터 전달 및 순서가 보장되지 않지만, 단순하고 빠름
    6. 정리
        1. IP와 거의 같다. + PORT + 체크섬 정도만 추가
        2. 애플리케이션에서 추가 작업 필요
        3. 요새 HTTP3로 넘어오면서 TCP의 3 way handshake 같이 ‘연결이 되었는지 요청하고 응답받고 이런 것을 줄이자’ 라고 하면서 각광 받는중.

## 4. PORT - 같은 IP 내에서 프로세스 구분

![Untitled 10](https://user-images.githubusercontent.com/59176149/210565715-99437682-e9e6-46e7-ad8b-563b624fb6e6.png)

- 0 ~ 65535 할당 가능
- 0 ~ 1023: 잘 알려진 포트, 사용하지 않는 것이 좋음
- FTP - 20, 21
- TELNET - 23
- HTTP - 80
- HTTPS - 443

## 5. DNS

- IP는 기억하기 어렵고 변경될 수 있다.
    
    ![Untitled 11](https://user-images.githubusercontent.com/59176149/210565718-01ba87ed-0417-4a24-b1fc-97ad86926cf0.png)

 

# [URI와 웹 브라우저 요청 흐름]

## 1. URI(Uniform Resource Identifier)

<img width="350" alt="Untitled 12" src="https://user-images.githubusercontent.com/59176149/210565720-108b6a20-5741-4fb4-91a7-78a695f29038.png">

[http://www.ietf.org/rfc/rfc3986.txt](http://www.ietf.org/rfc/rfc3986.txt) - 1.1.3. URI, URL, and URN

<img width="242" alt="Untitled 13" src="https://user-images.githubusercontent.com/59176149/210565722-b7150bc8-2bfb-4a18-9e8d-9c8054339b23.png">

- **U**niform: 리소스 식별하는 통일된 방식
- **R**esource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)
- **I**dentifier: 다른 항목과 구분하는데 필요한 정보

- URL 전체 문법

<img width="250" alt="Untitled 14" src="https://user-images.githubusercontent.com/59176149/210565724-96bbca88-c934-479a-86e9-ec0f9d9f2da8.png">

- 프로토콜(https) - scheme
    - 주로 프로토콜 사용
    - 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
        
        예) http, https, ftp 등등
        
    - http는 80 포트, https는 443 포트를 주로 사용, 포트는 생략 가능
    - https는 http에 보안 추가(HTTP Secure)
- 호스트명(www.google.com)
    - 도메인명 or IP주소를 직접 사용가능
- 포트번호(443)
    - 접속포트
    - 일반적으로 생략, 생략시 http는 80, https는 443
- 패스(/search)
    - 리소스 경로(path), 계층적 구조
    - 예) /home/file1.jpg, /members, / members/100, items/iphone12
- 쿼리 파라미터(q=hello&hl=ko)
    - key=value 형태
    - ?로 시작, &로 추가 가능 ?keyA=valueA&keyB=valueB
    - query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자형태

## 2. 웹 브라우저 요청 흐름

<img width="363" alt="Untitled 15" src="https://user-images.githubusercontent.com/59176149/210565726-f18c598b-e855-415a-8522-c34ff0b2c556.png">

<img width="253" alt="Untitled 16" src="https://user-images.githubusercontent.com/59176149/210565729-740bb00a-7778-4207-a764-0234eab79f17.png">

<img width="320" alt="Untitled 17" src="https://user-images.githubusercontent.com/59176149/210565732-d2c9a59f-e50d-4580-9e7b-97c520bc2c8b.png">

# [HTTP 기본]

## 1. 모든 것이 HTTP

- **H**yper**T**ext **T**ransfer **P**rotocol
    
    ex) HTML, TEXT
    
    IMAGE, 음성, 영상, 파일
    
    JSON, XML(API)
    
    거의 모든 형태의 데이터 전송기능
    
    서버간에 데이터를 주고 받을 때도 대부분 HTTP 사용
    
    **지금은 HTTP 시대!**
    
- HTTP 특징
    - 클라이언트 서버구조
        - Request Response 구조
        - 클라이언트는 서버에 요청을 보내고, 응답을 대기
        - 서버가 요청에 대한 결과를 만들어서 응답
    
    <img width="289" alt="Untitled 18" src="https://user-images.githubusercontent.com/59176149/210565734-d2f957d0-0a66-4952-9803-4bd8bd1465a2.png">
    
    서버 : 비지니스 로직, 데이터는 다 밀어 넣는다. 복잡한 비즈니스 로직은 서버에서 다 처리하게 둔다.
    
    클라이언트 : UI그리고 사용성에 집중
    
    → 클라이언트 서버 각각 독립적 진화가능
    
    - 무상태 프로토콜(스테이트리스), 비연결성
        - 서버가 클라이언트의 상태를 보존X
        - 장점: 서버 확장성 높음(스케일 아웃)
        - 단점: 클라이언트가 추가 데이터 전송
    - HTTP 메시지
    - 단순함, 확장가능
- 비연결성(connectionless)
    - HTTP는 기본이 연결을 유지하지 않는 모델
    - 일반적으로 초 단위의 이하의 빠른 속도로 응답
    - 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이하로 매우 작음
        - 예) 웹 브라우저에서 계속 연속해서 검색 버튼을 누르지는 않는다
    - 서버 자원을 매우 효율적으로 사용할 수 있음.
    - 한계와 극복
        - TCP/IP 연결을 새로 맺어야 함 - 3 way handshake 시간 추가
        - 웹 브라우저로 사이트를 요청하면 HTML뿐만 아니라 자바스크립트, css, 추가 이미지등등 수많은 자원이 함께 다운로드
        - 지금은 HTTP 지속 연결(Persistent Connections)로 문제 해결
        - HTTP/2, HTTP/3 에서 더 많은 최적화

## [HTTP 메서드]

- API URI 설계
    - 리소스 식별, URI 계층 구조 활용
    - **URI는 리소스만 식별!**
    - 리소스와 해당 리소스를 대상으로 하는 행위를 분리
        - 리소스: 회원
        - 행위: 조회, 등록, 삭제, 변경
    - 리소스는 명사, 행위는 동사 (미네랄을 캐라)
    - 행위(메서드)는 어떻게 구분? ex) GET, POST 등등
    
    ex)
    
    - **회원** 목록 조회 /members
    - **회원** 조회 /members/{id}
    - **회원** 등록 /members/{id}
    - **회원** 수정 /members/{id}
    - **회원** 삭제 /members/{id}
- HTTP 메서드
    - GET: 리소스조회
    - POST: 요청 데이터 처리, 주로 등록에 사용
        1. 새 리소스 생성(등록)
            - 서버가 아직 식별하지 않은 새 리소스 생성
        2. 요청 데이터 처리
            - 단순히 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야 하는 경우
            - 예) 주문에서 결제완료 → 배달시작 → 배달완료 처럼 단순히 값 변경을 넘어 프로세스의 상태가 변경되는 경우
            - POST의 결과로 새로운 리소스가 생성되지 않을 수도 있음
            - 예) POST /orders/{orderId}/start-delivery **(컨트롤 URI)**
        3. 다른 메서드로 처리하기 애매한 경우
            - 예) JSON으로 조회 데이터를 넘겨야 하는데, GET 메서드를 사용하기 어려운 경우
            - 애매하면 POST
    - PUT: 리소스를 대체, 해당 리소스가 없으면 생성
        - 쉽게 이야기해서 덮어버림
        - **중요! 클라이언트가 리소스를 식별**
        - 클라이언트가 리소스 위치를 알고 URI 지정
        - POST와 차이점
    - PATCH: 리소스 부분 변경
    - DELETE: 리소스 삭제

## [HTTP 메서드의 속성]

<img width="527" alt="Untitled 19" src="https://user-images.githubusercontent.com/59176149/210565739-3f1b0ed3-97e2-4c18-85a0-facba5b49d6c.png">

- 안전(safe)
    - 호출해도 리소스를 변경하지 않는다.
    - Q: 그래도 계속 호출해서, 로그 같은게 쌓여서 장애가 발생하면요?
    - A: 안전은 해당 리소스만 고려한다. 그런 부분까지 고려하지 않는다.
- 멱등(Idempotent)
    - f(f(x)) = f(x)
    - 한 번 호출 하든 두 번 호출 하든 100번 호출 하든 결과가 똑같다.
    - 멱등 메서도
        - GET: 한 번 조회하든, 두 번 조회하든 같은 결과가 조회된다.
        - PUT: 결과를 대체한다. 따라서 같은 요청을 여러번 해도 최종 결과는 같다.
        - DELETE: 결과를 삭제한다. 같은 요청을 여러번 해도 삭제된 결과는 똑같다.
        - POST: 멱등이 아니다! 두 번 호출하면 같은 결제가 중복해서 발생할 수 있다
    - 활용
        - 자동 복구 메커니즘
        - 서버가 TIMEOUT 등으로 정상 응답을 못주었을때, 클라이언트가 같은 요청을 다시 해도 되는가? 판단 근거
    - Q: 재요청 중간에 다른 곳에서 리소스를 변경해버리면?
        - 사용자1: GET → username:A, age:20
        - 사용자2: PUT → username:A, age:30
        - 사용자1: GET → username:A, age:30
    - A: 멱등은 외부 요인으로 중간에 리소스가 변경되는 것 까지는 고려하지는 않는다.
- 캐시가능(Cacheable)
    - 응답 결과 리소스를 캐시해서 사용해도 되는가?
    - GET, HEAD, POST, PATCH 캐시가능
    - 실제로는 GET, HEAD 정도만 캐시로 사용
        - POST, PATCH는 본문 내용까지 캐시 키로 고려해야 하는데, 구현이 쉽지 않음.
        

## [HTTP 메서드 활용]

### 클라이언트에서 서버로 데이터전송

- 데이터 전달 방식은 크게 2가지

- 쿼리 파라미터를 통한 데이터 전송
    - GET
    - 주로 정렬 필터(검색어)
- 메시지 바디를 통한 데이터 전송
    - POST, PUT, PATCH
    - 회원 가입, 상품 주문, 리소스 등록, 리소스 변경

- 4가지 상황

- 정적 데이터 조회
    - 이미지, 정적 텍스트 문서
    - 조회는 GET 사용
    - 정적 데이터는 일반적으로 쿼리 파라미터 없이 리소스 경로로 단순하게 조회 가능
- 동적 데이터 조회
    - 주로 검색, 게시판 목록에서 정렬 필터(검색어)
    - 조회 조건을 줄여주는 필터, 조회 결과를 정렬하는 정렬 조건에 주로 사용
    - 조회는 GET 사용
    - GET은 쿼리 파라미터 사용해서 데이터를 전달
    

## HTTP API 설계 예시

### HTTP API - 컬렉션

- POST 기반 등록
- 예) 회원관리 API 제공
    
    회원 목록 /members → GET
    
    회원 등록 /members → POST
    
    회원 조회 /members/{id} → GET
    
    회원 수정 /members/{id} → PATCH, PUT, POST
    
    회원 삭제 /members/{id} → DELETE
    

### HTTP API - 스토어

- PUT 기반 등록
- 예) 정적 컨테츠 관리, 원격 파일 관리

### HTML FORM 사용

- 웹 페이지 회원 관리
- GET, POST만 지원

## HTTP 상태코드 소개

클라이언트가 보낸 요청의 처리 상태를 응답에서 알려주는 기능

- 1xx(Informational): 요청이 수신되어 처리중
- 2xx(Successful): 요청 정상 처리
- 3xx(Redirection): 요청을 완료하려면 추가 행동이 필요
    - 301
    - 308
    - 302
    - 307
    - 303

- 4xx(Client Error): 클라이언트 오류, 잘못된 문법등으로 서버가 요청을 수행X
    - 401 Unauthorized
        - 인증 되지 않음
        - 401 오류 발생시 응답에  WWW-Authenticate 헤더와 함께 인증 방법을 설명
        - 참고
            - 인증(Authentication): 본인이 누구인지 확인, (로그인)
            - 인가(Authorization): 권한부여 (ADMIN 권한처럼 특정 리소스에 접근할 수 있는 권한, 인증이 있어야 인가가 있음)
            - 오류 메시지가 Unauthorized 이지만 인증 되지 않음(이름이 아쉬움)
    - 403 Forbidden - (서버가 요청을 이해했지만 승인을 거부함)
        - 주로 인증 자격 증명은 있지만, 접근 권한이 불충분한 경우
        - 예) 어드민 등급이 아닌 사용자가 로그인은 했지만, 어드민 등급의 리소스에 접근하는 경우
    - 404 Not Found - (요청 리소스를 찾을수 없음)
        - 요청 리소스가 서버에 없음
        - 또는 클라이언트가 권한이 부족한 리소스에 접근 할때 해당 리소스를 숨기고 싶을때
- 5xx(Server Error): 서버 오류, 서버가 정상 요청을 처리하지 못함
    - 500 Internal Server Error
        - 서버 내부 문제로 오류 발생
        - 애매하면 500 오류
    - 503 Service Unavailable
        - 서버가 일시적은 과부하 또는 예정된 작업으로 잠시 요청을 처리할 수 없음
        - Retry-After 헤더 필드로 얼마뒤에 복구 되는지 보낼 수도 있음.
        - 

## HTTP 헤더 - 일반헤더

- HTTP 전송에 필요한 모든 부가정보
- 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트, 서버정보, 캐시 관리 정보
- 표준 헤더가 너무 많음
    - https://en.wikipedia.org/wiki/List_of_HTTP_header_fields
- 필요시 임의의 헤더 추가 가능
    - helloworld: hihi