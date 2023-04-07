# OAuth2.0 개념과 동작에 대해 알아보자

### 목차

1. [OAuth란?](#1-oauth란)
2. [등록](#2-등록)
    
    2-1. [구글 등록 테스트](#2-1-구글-등록-테스트)
    
3. [OAuth 2.0 Flow](#3-oauth-20-flow)

### 1. OAuth란?

- OAuth(**O**pen **Auth**orization)
- 인가 프레임워크는 리소스 소유자와 HTTP 서비스 간의 승인 상호 작용을 조정하거나 타사 응용프로그램이 자체적인 액세스를 획득하도록 허용함으로써 타사 응용 프로그램이 리소스 소유자를 대신하여 HTTP 서비스에 대한 제한된 액세스를 얻을 수 있도록 해준다.

<img width="385" alt="image" src="https://user-images.githubusercontent.com/59176149/230622513-72e86650-e0bd-45a4-a408-8a0f13570425.png">

- 위 그림과 같이 구글, 페이스북, 네이버 로그인 같은 소셜 로그인들은 OAuth2 프로토콜 기반의 인증방식을 지원한다.
- 연동되는 외부 웹 어플리케이션에서 페이스북과 트위터 등이 제공하는 기능을 간편하게 사용할 수 있다.
- 즉, OAuth는 다른 서비스의 회원정보를 안전하게 사용하기 위한 방법이라고 생각하자.
- 사용자가 자신의 소셜 아이디/비밀번호를 우리 서비스에 알려주지 않아도, 고객의 정보를 우리 서비스에서 안전하게 사용하기 위한 방법이다.

### 2. 등록

- 서비스마다 등록하는 방법이 모두 다르지만 공통적인 것은 아래 그림과 같다.

<img width="667" alt="image" src="https://user-images.githubusercontent.com/59176149/230622566-eb6a5479-b331-4b21-85df-4e8b2c72fceb.png">

- Client ID
    - 우리가 만든 Application을 식별하는 식별자
- Client Secret
    - 외부에 절대 노출되서는 안된다
- Authorized redirect URIs
    - Resource Server만 갖는 정보로, client에 권한을 부여하는 과정에서 나중에 Authorized code를 전달하는 통로다.

### 2-1. 구글 등록 테스트

- [https://console.cloud.google.com/](https://console.cloud.google.com/)
- 위 사이트를 접속해서 만들어보자.

<img width="578" alt="image" src="https://user-images.githubusercontent.com/59176149/230622649-caec2b69-6748-460c-a05d-6b65a2d8f394.png">

- My Project에 새프로젝트를 만들어준다.

<img width="550" alt="image" src="https://user-images.githubusercontent.com/59176149/230622709-eb71b0ed-37a9-42f1-a555-7a5b0c9b7918.png">

- 다음과 같이 새프로젝트이름을 지정하고 만들기를 클릭한다.

<img width="446" alt="image" src="https://user-images.githubusercontent.com/59176149/230622908-856059f9-545e-45d2-806f-85532f6f60cf.png">

- 햄버거 박스를 눌러 API 및 서비스 > 사용자 인증 정보를 클릭한다.

<img width="692" alt="image" src="https://user-images.githubusercontent.com/59176149/230623002-0bbd23bf-bb25-4d6c-971b-2670540a4878.png">


- 사용자 인증 정보 만들기에 OAuth 클라이언트 ID를 클릭해준다.

<img width="433" alt="image" src="https://user-images.githubusercontent.com/59176149/230623077-29931893-d243-4865-8f51-84902e1a422c.png">


- 애플리케이션 유형과 이름을 정해서 만들기 버튼을 클릭한다.

<img width="447" alt="image" src="https://user-images.githubusercontent.com/59176149/230623157-22829f67-183a-4b46-872f-a34f966cd811.png">


- [초반에 나온 그림](#2-등록) 처럼 클라이언트 ID와 비밀번호(Secret)가 생성되며 Authorized redirect URIs 것을 볼 수 있다.

### 3. ****OAuth 2.0 Flow****

<img width="672" alt="image" src="https://user-images.githubusercontent.com/59176149/230623309-7b1ad7ad-be6d-409b-aa1a-915d492e461e.png">


1. 사용자(Resource Owner)는 서비스(client)를 이용하기 위해 로그인 페이지에 접근한다.
2. 그럼 서비스(client)는 사용자(Resource Owner)에게 로그인 페이지를 제공하게 된다. 로그인 페이지에서 사용자는 "페이스북/구글 으로 로그인" 버튼을 누른다.
3. 만일 사용자가 Login with Facebook 버튼을 클릭하게 되면, 특정한 url 이 페이스북 서버쪽으로 보내지게 된다.

<img width="679" alt="image" src="https://user-images.githubusercontent.com/59176149/230623387-b8b52064-bfe8-499c-b5d5-ce8c9df354e8.png">

<img width="670" alt="image" src="https://user-images.githubusercontent.com/59176149/230623420-68548356-2101-4c85-8761-494bcdc5ef62.png">

- gitlab 사이트에서 구글 로그인을 할때 url을 복사해서 내용을 확인했을때 아래와 같다

```
https://accounts.google.com/o/oauth2/auth/identifier?access_type=offline&
**client_id**=805818759045-aa9a2emskmnmeii44krng550d2fd44ln.apps.googleusercontent.com&
**redirect_uri**=https://gitlab.com/users/auth/google_oauth2/callback&
response_type=code&
**scope**=email profile&
state=79d206dc2e07b33197ca9e4c13f15876856ba488a570dde9&
service=lso&
o2v=1&
flowName=GeneralOAuthFlow
```

- 향후 **redirect_uri 경로를 통해서 Resource Server는 client에게 임시비밀번호인 Authorization code를 제공**한다.
4. 클라이언트로부터 보낸 서비스 정보와, 리소스 로그인 서버에 등록된 서비스 정보를 비교한다.

<img width="681" alt="image" src="https://user-images.githubusercontent.com/59176149/230623542-fad2d2a8-9280-431c-9e0f-b6eead0b0b7f.png">


4-1. 확인이 완료되면, Resource Server로 부터 전용 로그인 페이지로 이동하여 사용자에게 보여준다.

<img width="662" alt="image" src="https://user-images.githubusercontent.com/59176149/230623643-360e3967-7756-4f63-bbfe-724972dcff53.png">

5. ID/PW를 적어서 로그인을 하게되면, client가 사용하려는 기능(scope)에 대해 Resource Owner의 동의(승인)을 요청한다.

<img width="670" alt="image" src="https://user-images.githubusercontent.com/59176149/230623708-22aacee0-c4b3-4d37-b00d-18047453502f.png">

<img width="671" alt="image" src="https://user-images.githubusercontent.com/59176149/230623762-ca2427a5-717f-458f-80be-c144a1139df7.png">

5-1. 사용자가 허용 버튼 누르면 사용자는 권한을 위임했다는 승인이 Server 에 전달된다. 

<img width="360" alt="image" src="https://user-images.githubusercontent.com/59176149/230623841-c7c03797-7ab7-4c69-bbcc-0719ba35a5c4.png">


허용한 사용자의 user id를 1이라고 칭한다면 서버는 이와 같은 정보를 가지게 된다.
```
1. Client Id : Resource Owner와 연결된 client가 누군지
2. Client Secret: Resource Owner와 연결된 client의 비밀번호
3. Redirect URL : (진짜)client와 통신할 통로
4. user id : client와 연결된 Resource Owner의 id
5. scope : client가 Resource Owner 대신에 사용할 기능들
```

6. 하지만, 이미 Owner가 Client에게 권한 승인을 했더라도 아직 Server가 허락하지 않았다. 따라서, Resource Server 도 Client에게 권한 승인을 하기위해 Authorization code(임시 비밀번호)를 Redirect URL을 통해 사용자에게 응답하고
7. Owner는 서버에게 받은 Location 헤더 값을 통해 url을 이동하고 클라이언트는 Authorization Code를 얻게 된다.

<img width="620" alt="image" src="https://user-images.githubusercontent.com/59176149/230624097-40421ca2-2a60-4003-a6c7-129fba8180d2.png">


<img width="360" alt="image" src="https://user-images.githubusercontent.com/59176149/230624142-20bb6a31-ad72-44d0-a2ae-39d33c863f00.png">


<img width="670" alt="image" src="https://user-images.githubusercontent.com/59176149/230624174-b5573790-4b6f-4669-92ea-7fb19a8c9673.png">


8. 이제 Client가 Resource Server에게 직접 url(클라이언드 아이디, 비번, 인증코드 등)을 보낸다.

<img width="448" alt="image" src="https://user-images.githubusercontent.com/59176149/230624275-e99fd4d1-ff46-42d3-9f40-f0c2b57bc058.png">

<img width="659" alt="image" src="https://user-images.githubusercontent.com/59176149/230624330-ed9e01f7-0263-4bce-bae1-46f937ffa813.png">


9. 그럼 Resource Server는 Client가 전달한 정보들을 비교해서 일치한다면, Access Token을 발급한다. 그리고 이제 필요없어진 Authorization code는 지운다.
10. 그렇게 토큰을 받은 Client는 사용자에게 최종적으로 로그인이 완료되었다고 응답한다.

<img width="490" alt="image" src="https://user-images.githubusercontent.com/59176149/230624427-96c98379-4ad4-4496-a7ed-43b1f9c14bb7.png">

<img width="550" alt="image" src="https://user-images.githubusercontent.com/59176149/230624503-ab4f5162-d350-4c48-af8e-2618d5d76667.png">


11 ~ 14. 이제 client는 Resource server의 api를 요청해 Resource Owner의 ID 혹은 프로필 정보를 사용할 수 있다.

<img width="490" alt="image" src="https://user-images.githubusercontent.com/59176149/230624577-1b435b96-c358-40ed-a9b5-a42c71e25f77.png">

<img width="670" alt="image" src="https://user-images.githubusercontent.com/59176149/230624643-d979f55e-739b-4169-86e2-b99e07f83e2b.png">

15. Access Token이 기간이 만료되어 401에러가 나면, Refresh Token을 통해  Access Token을 재발급 한다.

<img width="655" alt="image" src="https://user-images.githubusercontent.com/59176149/230624694-543ada50-648c-4582-8723-2233035a8a5b.png">


- Refresh Token
    - *Refresh Token의 발급 여부와 방법 및 갱신 주기 등은 OAuth를 제공하는 Resource Server마다 상이하다*
    - Access Token은 만료 기간이 있어, 만료된 Access Token으로 API를 요청하면 401 에러가 발생한다. Access Token이 만료되어 재발급받을 때마다 서비스 이용자가 재 로그인하는 것은 다소 번거롭다.
    - 보통 Resource Server는 Access Token을 발급할 때 Refresh Token을 함께 발급한다. Client는 두 Token을 모두 저장해두고, Resource Server의 API를 호출할 때는 Access Token을 사용한다. Access Token이 만료되어 401 에러가 발생하면, Client는 보관 중이던 Refresh Token을 보내 새로운 Access Token을 발급받게 된다.

### 참고

- [https://opentutorials.org/course/3405](https://opentutorials.org/course/3405)
- [https://inpa.tistory.com/entry/WEB-📚-OAuth-20-개념-💯-정리](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-OAuth-20-%EA%B0%9C%EB%85%90-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC)
- [https://tecoble.techcourse.co.kr/post/2021-07-10-understanding-oauth/](https://tecoble.techcourse.co.kr/post/2021-07-10-understanding-oauth/)
