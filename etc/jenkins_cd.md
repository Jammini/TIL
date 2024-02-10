# 젠킨스를 통한 CI/CD 구성하기 - CD

### 목차

1. [개요](#1-개요)
2. [CD 파이프라인 워크플로우](#2-cd-파이프라인-워크플로우)
3. [Naver Could Container Registry 신청](#3-naver-could-container-registry-신청)
4. [Jenkins서버 ↔ 애플리케이션 서버 ssh 키 생성](#4-jenkins서버-↔-애플리케이션-서버-ssh-키-생성)
5. [Jenkins ↔ Application Server SSH 원격 접속 Credentials 등록](#5-jenkins-↔-application-server-ssh-원격-접속-credentials-등록)
6. [Jenkins ↔ Docker Registry Credentials 등록](#6-jenkins-↔-docker-registry-credentials-등록)

### 1. 개요

지난 젠킨스를 이용하여 CI를 구성하였고 이번 글에서는 배포를 자동화해주는 CD를 구축해보려한다.

우리는 CI/CD 구축이 되어 있지 않다면 매번 코드를 옮기고 Jar를 빌드해서 서버에 옮기고 서버를 재시작하는 과정을 반복해야 할 것이다.

Jenkins를 이용해 구축할 것이며 아래 과정에서 천천히 살펴보자.

### 2. CD 파이프라인 워크플로우

<img width="706" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/02cb48db-698d-4543-962a-1959d99e8e3c">


1. Github에 변경사항에 대해 Push후 PR를 올린다.
2. Github에서 레포지토리의 main브랜치에 코드가 merge되면 Jenkins에게 webhook을 날린다.
3. Jenkins가 빌드, 도커 이미지를 생성한다.
4. 생성된 도커 이미지를 Docker Registry에 등록한다.
5. auctionHub 서버가 도커 이미지를 받아 실행한다.

### 3. Naver Could Container Registry 신청

<img width="700" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/8093e628-5764-456f-88cb-bcd4ee28867d">

애플리케이션의 이미지를 저장할 서버로 Container Registry를 신청해준다. Docker Conainer 이미지를 저장하고 관리하는 서버로 사용할 수 있다.

https://guide.ncloud-docs.com/docs/containerregistry-start

### 4. Jenkins서버 ↔ 애플리케이션 서버 ssh 키 생성

젠킨스에서 애플리케이션 서버간 ssh 통신이 가능하도록 설정이 필요하다.

1. Jenkins 서버 ssh 키 생성하기

```java
ssh-kegen -t rsa
```

<img width="705" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/af2841f6-dea5-474d-841f-e689d74ece71">

2. Application 서버 ssh 키 생성

```java
ssh-kegen -t rsa -f id_rsa
```

<img width="705" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/d20dffed-db69-4d9a-bd4e-a40e928fbf1f">

3. Jenkins서버에서 Application Server를 Known_hosts로 등록하기

```java
ssh-keyscan -H [Application 서버 ip] >> ~/.ssh/known_hosts
```

4. Application 서버의 authorized_keys에 Jenkins 서버의 공개키(id_rsa.pub) 입력하기

<img width="710" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/f83ff756-9347-4778-a17e-1da258eadcc5">

위 나온 key를 vi를 통해 편집하여 저장하고 닫는다.

<img width="704" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/31b1acd8-583e-4625-93cc-b10f27263b53">

### 5. Jenkins ↔ Application Server SSH 원격 접속 Credentials 등록

1. Jenkins 서버에서 `cat ~/.ssh/id_rsa` 명령어를 입력하여 출력된 비밀키를 복사한다.
    - 이때 주의할 점으로는 BEGIN행부터 END행까지 포함해서 넣어주어야 한다.

<img width="704" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/0fe8c0ae-a604-4f04-bdef-3c2bd9dfb9f1">

2. Jenkins로 넘어가 새로운 Credentials를 추가해준다.

<img width="577" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/1a6567e5-9812-4c16-813a-613666ae1915">

ID 에는 Jenkins 시스템 내에서 해당 Credentials를 구분할 ID 값을 적어준다.

Username 에는 Application Server에서 사용되는 OS 계정명을 적는다. (ex. root)

복사한 비밀키 값을 Private key 란에 입력한다.

### 6. Jenkins ↔ Docker Registry Credentials 등록

1. 네이버 클라우드 포탈에서 신규 API 인증키를 생성한다.

<img width="705" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/2e85bb62-bc0e-454b-92c7-11ded2c1f77b">

2. 발급받은 API 인증키를 앞선 과정과 똑같이 Jenkins 서버의 Credentials로 등록한다.
- Kind는 Username with password 를 선택한다.
- Username 에는 API 인증키의 Access Key ID 입력한다.
- Password 에는 API 인증키의 Secret Key 입력한다.
- ID에는 Jenkins 시스템 내에서 해당 Credentials를 구분할 ID 값을 적어준다.

<img width="618" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/19ad7adb-b71f-4cea-82c2-d1f18e1d6a30">
