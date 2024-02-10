# 젠킨스를 통한 CI/CD 구성하기 - CD

### 목차

1. [개요](#1-개요)
2. [CD 파이프라인 워크플로우](#2-cd-파이프라인-워크플로우)
3. [Naver Could Container Registry 신청](#3-naver-could-container-registry-신청)
4. [Jenkins서버 ↔ 애플리케이션 서버 ssh 키 생성](#4-jenkins서버-↔-애플리케이션-서버-ssh-키-생성)
5. [Jenkins ↔ Application Server SSH 원격 접속 Credentials 등록](#5-jenkins-↔-application-server-ssh-원격-접속-credentials-등록)
6. [Jenkins ↔ Docker Registry Credentials 등록](#6-jenkins-↔-docker-registry-credentials-등록)
7. [Dockerfile 생성하기](#7-dockerfile-생성하기)
8. [배포 작업하기](#8-배포-작업하기)
9. [파이프라인 스크립트 작성](#9-파이프라인-스크립트-작성)
10. [main 브랜치 Push 및 배포 확인하기](#10-main-브랜치-push-및-배포-확인하기)

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

### 7. Dockerfile 생성하기

도커는 이미지를 빌드할 때, DockerFile이라는 스크립트 파일 기반으로 동작한다.

프로젝트 최상단에 Dokerfile을 아래 공식문서를 보고 참고하길 바란다.

https://docs.docker.com/engine/reference/builder/

### 8. 배포 작업하기

Github에서 webhook을 이용하여 main브랜치에 push가 된다면, Jenkins 서버의 API가 호출 되도록 하였다.

1. Jenkins Generic Webhook Trigger Plugin 설치

<img width="707" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/282d621b-9c5c-4f62-988f-ce3cdd231b1e">

2. 파이프라인을 만들고 해당 플러그인을 체크해주면 배포 작업이 유발된다.
    
    <img width="376" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/19488895-0722-4b19-b544-42b2533dc318">
    
3. Generic Webhook설정을 통해 구체적으로 어떤 상황에서 배포 작업이 유발될지 설정해준다.
4. main 브랜치에 코드가 Push 되면 Github에서는 다음과 같은 API를 호출한다.

```
http://JENKINS_URL/generic-webhook-trigger/invoke
```

5. Github 레포지토리에 돌아가 webhook을 등록해준다.

<img width="621" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/d709bbdc-9caa-4d30-8e77-2a220b17c268">

이렇게 하고 저장을 해주면 Pushes 이벤트가 발생할 때 마다 해당 Payload URL을 호출한다.

### 9. 파이프라인 스크립트 작성

webhook 조건을 만족할 경우, 파이프라인 스크립트가 동작해서 배포 자동화 작업을 수행한다.

```
pipeline {
  environment {
          IMAGE_NAME = "이미지 이름"
          REGISTRY_CONTAINER = "레지스트리 컨테이너 endpoint"
          REGISTRY_CREDENTIAL = "젠킨스 <-> 도커 Credentials ID값"
          SSH_CONNECTION_IP = "계정명@Application서버IP"
          SSH_CONNECTION_CREDENTIAL = "젠킨스 <-> Application Server SSH Credentials ID"
      }

    agent any

    stages {
        // Checkout Git repository
        stage('Checkout Git') {
            steps {
                git branch: 'main',
                credentialsId: '02247ad5-1f10-471b-ab72-6d95b8d2d293D',
                url: 'https://github.com/f-lab-edu/auctionHub.git'
            }
        }

        // Build repository
        stage('Build') {
            steps {
                sh './gradlew clean bootJar'
                echo 'Build repository Success!'
            }
        }

        // Test
        stage('Test') {
            steps {
                // sh './gradlew test'
                echo 'Test Success!'
            }
        }

        // 도커 이미지 빌드
        stage('Build Container Image') {
            steps {
                script {
                    image = docker.build("${REGISTRY_CONTAINER}/${IMAGE_NAME}")
                }
            }
        }

        // 생성된 도커 이미지를 네이버 클라우드 Docker Registry에 등록
        stage('Push Container Image') {
            steps {
                script {
                    docker.withRegistry("https://${REGISTRY_CONTAINER}", REGISTRY_CREDENTIAL) {
                        image.push("${env.BUILD_NUMBER}")
                        image.push("latest")
                        image
                    }
                }
            }
        }

        // 애플리케이션 서버가 도커 이미지를 다운받고 실행하도록 제어
        stage('Server run') {
            steps {
                sshagent(credentials: [SSH_CONNECTION_CREDENTIAL]) {
                    sh "ssh -o StrictHostKeyChecking=no ${SSH_CONNECTION_IP} 'whoami'"
                    // 최신 컨테이너 삭제
                    sh "ssh -o StrictHostKeyChecking=no ${SSH_CONNECTION_IP} 'docker rm -f ${IMAGE_NAME}'"
                    // 최신 이미지 삭제
                    sh "ssh -o StrictHostKeyChecking=no ${SSH_CONNECTION_IP} 'docker rmi -f ${REGISTRY_CONTAINER}/${IMAGE_NAME}:latest'"
                    // 최신 이미지 PULL
                    sh "ssh -o StrictHostKeyChecking=no ${SSH_CONNECTION_IP} 'docker pull ${REGISTRY_CONTAINER}/${IMAGE_NAME}:latest'"
                    // 이미지 확인
                    sh "ssh -o StrictHostKeyChecking=no ${SSH_CONNECTION_IP} 'docker images'"
                    // 최신 이미지 RUN
                    sh "ssh -o StrictHostKeyChecking=no ${SSH_CONNECTION_IP} 'docker run -d --name ${IMAGE_NAME} -p 8080:8080 ${REGISTRY_CONTAINER}/${IMAGE_NAME}:latest'"
                    // 컨테이너 확인
                    sh "ssh -o StrictHostKeyChecking=no ${SSH_CONNECTION_IP} 'docker ps'"
                }
            }
        }
    }
}
```

### 10. main 브랜치 Push 및 배포 확인하기

<img width="705" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/5d1ce4c2-2637-43f2-9fb3-0ed3718a222e">


### 참고
- https://guide.ncloud-docs.com/docs/containerregistry-start
- https://docs.docker.com/engine/reference/builder/
- https://cookie-dev.tistory.com/21
- [https://velog.io/@doyuni/Jenkins-NAVER-Cloud-Platform-Docker로-CICD-무중단-배포-환경-구축하기-2편-7rk4w9eynh](https://velog.io/@doyuni/Jenkins-NAVER-Cloud-Platform-Docker%EB%A1%9C-CICD-%EB%AC%B4%EC%A4%91%EB%8B%A8-%EB%B0%B0%ED%8F%AC-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0-2%ED%8E%B8-7rk4w9eynh)
- [https://devbksheen.tistory.com/entry/Jenkins를-이용한-Docker-컨테이너-자동-배포하기Blue-Ocean-NCP](https://devbksheen.tistory.com/entry/Jenkins%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-Docker-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%EC%9E%90%EB%8F%99-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0Blue-Ocean-NCP)