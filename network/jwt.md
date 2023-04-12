# JWT(JSON Web Token)란?

### 목차

1. [JWT란?](#1-jwt란)
2. [JWT 사용시기](#2-jwt-사용시기)
3. [JWT구조](#3-jwt구조)
4. [JWT 이용한 인증 과정](#4-jwt-이용한-인증-과정)
5. [JWT 장점](#5-jwt-장점)

### 1. JWT란?

- JWT(JSON Web Token)란 **인증에 필요한 정보들을 암호화시킨 JSON 토큰**
을 의미한다. 그리고 JWT 기반 인증은 JWT 토큰(Access Token)을 HTTP 헤더에 실어 서버가 클라이언트를 식별하는 방식이다
- JWT는 JSON 데이터를 Base64 URL-safe Encode 를 통해 인코딩하여 직렬화한 것이며, 토큰 내부에는 위변조 방지를 위해 개인키를 통한 **전자서명**도 들어있다. 따라서 사용자가 JWT 를 서버로 전송하면 서버는 서명을 검증하는 과정을 거치게 되며 검증이 완료되면 요청한 응답을 돌려준다.

### 2. ****JWT 사용시기****

1. Authorization

JWT를 사용하는 가장 일반적인 예시다.

사용자가 로그인 한 후 각 요청에는 JWT를 포함하여 사용자가 해당 토큰으로 허용된 경로, 서비스 및 리소스에 액세스할 수 있게 한다.

1.  Information Exchange

JWT는 클라이언트와 서버 간에 정보를 안전하게 전송하는 좋은 방법이다.

예를 들어 공개/개인 키 쌍을 사용하여 JWT에 서명할 수 있으므로 보낸 사람이 누구인지 확인할 수 있다. 또한 header와 payload를 사용하여 서명을 계산하므로 콘텐츠가 변조되지 않았음을 확인할 수도 있다.

### 3. JWT구조

JWT는 ( **.** )으로 구분된 세 부분으로 구성된다.

1. Header
2. Payload
3. Signature

![image](https://user-images.githubusercontent.com/59176149/231497925-5b86d252-529d-4127-a27a-379a945fb992.png)

1. Header

헤더는 *일반적으로* 토큰 유형(JWT)과 사용 중인 서명 알고리즘의 두 부분으로 구성된다.

- alg : 서명 암호화 알고리즘(ex: HMAC SHA256, RSA)
- typ : 토큰 유형
1. Payload

토큰에서 사용할 정보의 조각들인 Claim 이 담겨있다. (실제 JWT 를 통해서 알 수 있는 데이터)

즉, 서버와 클라이언트가 주고받는 시스템에서 실제로 **사용될 정보에 대한 내용**을 담고 있는 섹션이다.

*클레임에는 Registered claims, Public claims, Private claims* 의 세 가지 유형이 있습니다 .

2-1. Registed claims : 미리 정의된 클레임.

- iss(issuer; 발행자),
- exp(expireation time; 만료 시간),
- sub(subject; 제목),
- iat(issued At; 발행 시간),
- jti(JWI ID)

2-2. Public claims : 사용자가 정의할 수 있는 클레임 공개용 정보 전달을 위해 사용.

2-3. Private claims : 해당하는 당사자들 간에 정보를 공유하기 위해 만들어진 사용자 지정 클레임. 외부에 공개되도 상관없지만 해당 유저를 특정할 수 있는 정보들을 담는다

1. Signature

시그니처에서 사용하는 알고리즘은 헤더에서 정의한 알고리즘 방식(alg)을 활용한다.

시그니처의 구조는 (헤더 + 페이로드)와 서버가 갖고 있는 유일한 key 값을 합친 것을 헤더에서 정의한 알고리즘으로 암호화를 한다.

![image](https://user-images.githubusercontent.com/59176149/231497984-9d6bbd8c-123d-45de-9d18-790f29a40ffb.png)

예를 들어 헤더에서 HMAC SHA256 알고리즘을 사용한다고 지정을 했으면 시그니처는 다음과 같다.

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

마지막에 붙는 secret은 서버가 갖고 있는 key 값이다.

### 4. JWT 이용한 인증 과정

![image](https://user-images.githubusercontent.com/59176149/231498047-87f14077-e5d7-4a44-8002-2b4e4a4f59c5.png)

1. 사용자가 ID, PW를 입력하여 서버에 로그인 인증을 요청한다.
2. 서버에서 클라이언트로부터 인증 요청을 받으면, Header, PayLoad, Signature를 정의한다.Hedaer, PayLoad, Signature를 각각 Base64로 한 번 더 암호화하여 JWT를 생성하고 이를 쿠키에 담아 클라이언트에게 발급한다.
3. 클라이언트는 서버로부터 받은 JWT를 로컬 스토리지에 저장한다. (쿠키나 다른 곳에 저장할 수도 있음)API를 서버에 요청할때 **Authorization header에 Access Token을 담아**서 보낸다.
4. 서버가 할 일은 클라이언트가 Header에 담아서 보낸 JWT가 내 서버에서 발행한 토큰인지 일치 여부를 확인하여 일치한다면 인증을 통과시켜주고 아니라면 통과시키지 않으면 된다.인증이 통과되었으므로 페이로드에 들어있는 유저의 정보들을 select해서 클라이언트에 돌려준다.
5. 클라이언트가 서버에 요청을 했는데, 만일 액세스 토큰의 시간이 만료되면 클라이언트는 리프래시 토큰을 이용해서
6. 서버로부터 새로운 엑세스 토큰을 발급 받는다.

### 5. JWT 장점

- Header와 Payload를 가지고 Signature를 생성하므로 **데이터 위변조를 막을 수 있다.**
- 인증 정보에 대한 **별도의 저장소가 필요없다.**
- JWT는 토큰에 대한 기본 정보와 전달할 정보 및 토큰이 검증되었음을 증명하는 서명 등 필요한 모든 정보를 자체적으로 지니고 있다.
- 클라이언트 인증 정보를 저장하는 세션과 다르게, 가 되어 서버 확장성이 우수해질 수 있다. **서버는 무상태(StateLess)**
- 토큰 기반으로 **다른 로그인 시스템에 접근 및 권한 공유가 가능하다. (쿠키와 차이)**
- OAuth의 경우 Facebook, Google 등 소셜 계정을 이용하여 다른 웹서비스에서도 로그인을 할 수 있다.

### 참고

- [https://jwt.io/introduction](https://jwt.io/introduction)
- [https://cbw1030.tistory.com/331](https://cbw1030.tistory.com/331)
- [https://inpa.tistory.com/entry/WEB-📚-JWTjson-web-token-란-💯-정리](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-JWTjson-web-token-%EB%9E%80-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC)
