# 젠킨스를 통한 CI/CD 구성하기 - CI

### 목차

1. [개요](#1-개요)
2. [CI/CD란?](#2-cicd란)
3. [Jenkins 설정](#3-jenkins-설정)
4. [결론](#4-결론)

### 1. 개요

빌드를 자동화 하기위해 알아 보던 중 GithubAction, Jenkins, Travis 등등 다양한 CI 도구를 마주하게 되었다.

그 중 Jenkins와 GithubAction을 두고 고민을 했고 간단하게 보면, 아래와 같이 비교 할 수 있을 것 같다.

| Jenkins | GihubAction |
| --- | --- |
| 참고 자료가 많음 | 참고 자료가 상대적으로 적음 |
| CI/CD를 구성하기 어려움 | CI/CD를 구성하기 상대적으로 쉬움 |

위와 같이 나눠 볼 수 있었고, 그래도 써보지는 앉았지만 예전부터 많이 들어본 Jenkins로 선택하는 것으로 결론을 지었다.

### 2. CI/CD란?

간단하게 개념을 잡고 가면 CI/CD는 개발 - 빌드 - 테스트 - 배포까지의 과정을 자동화하는 것이라고 생각하면 된다.

**CI (Continuous Integration)란?**

여러 개발자가 작성하거나 수정한 소스를 지속적으로 통합하고 테스트하는 것을 뜻한다.

**CD (Continuous Delivery/Deployment)란?**

개발, 통합, 배포, 릴리즈, 테스트를 자동화하여 지속적으로 배포하는 것을 뜻한다.

### 3. Jenkins 설정

젠킨스 서버를 띄우기 위해 네이버 클라우드 플랫폼을 이용하였다.

https://guide.ncloud-docs.com/docs/devtools-devtools-1-1

네이버 클라우드 공식 가이드를 따라 젠킨스 서버 구축을 진행하였다. 가이드를 보고 설치가 꽤 걸리길래 잠시 다른일을 하고 한눈 판사이 설치화면이 지나가서 설치가 다 된줄 알았으나 일부 플러그인들이 정상적으로 설치가 안되어 있었다. 처음에는 그것도 모르고 일부 동작을 안하길래 엄청 헤맸다..

만약, 저처럼 일부 플러그인이 설치되지 않는다면, 터미널로 가서 Jenkins의 버전을 직접 업데이트 하는 것을 추천한다.

**JDK 버전 설정**

자신의 프로젝트 버전에 맞게 JDK를 설정해주자.

네이버 클라우드로 구축하면 기본적으로 JDK 1.8이 설치 되어 있는데 현재 프로젝트에서 java 17을 사용하고 있기 때문에 변경해주었다.

**Gradle 버전 설정**

자신의 프로젝트 버전에 맞게 gradle를 설정해주자.

현재 프로젝트에 맞춰 gradle-8.2.1을 다운 받아 설치해주었다.

**Github Access token 설정**

Github > Settings > Developer settings > Personal access tokens > Token (classic)에서 토큰을 생성한다.

<img width="517" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/97033972-14b9-461a-931a-0a02097e7fff">

**Multibranch Pipeline**

멀티 브랜치 파이프라인은 Jenkins에서 제공하는 Item의 종류 중 하나이다.

멀티 브랜치 파이프라인은 하나의 프로젝트의 여러 브랜치를 한 번에 대응할 수 있는 장점이 있다.

main 브랜치에 merge가 발생되면 main 브랜치에 해당하는 job이 수행된다.
release/1.0.1 브랜치가 새롭게 생성될 경우, release 브랜치에 해당하는 job이 수행된다.

같은 프로젝트임에도 개발, 운영 환경에 따라 새로운 Item을 설정해줘야 한다면,
공통으로 적용되어야 하는 부분에 일부 문법의 수정이 발생한다면,
각각의 Item을 모두 찾아가서 각각의 Jenkinsfile을 수정해야 하는 일이 발생할 수도 있게 된다.
그러나 멀티브랜치 파이프라인은 하나의 Jenkinsfile을 사용하기에 관리 포인트가 한 곳으로 응집된다.

**Multibranch Scan Webhook Trigger 플러그인 설치**

DashBoard > Jenkins 관리 > 플러그인 관리에서 Multibranch Scan Webhook Trigger를 설치해준다.

<img width="680" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/217bc833-1595-420f-929b-9b646803adf9">

간단하게, webhook이 날아왔을 때, 어떤 브랜치가 변경되었는지 확인하고, 해당 브랜치의 pipeline을 실행하도록하는 플러그인이다.

**Multibranch Pipeline 아이템 생성**

젠킨스에 들어가 새로운 아이템을 누르게되면 아래와 같은 Multibranch Pipeline을 생성 할 수 있게 된다. 이 Item을 선택하면 Jenkins 내부에 설정된 Jenkinsfile을 통해 빌드를 수행할 수 있다.

<img width="405" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/7b8983f5-2926-4ef8-9e43-4c70b4237399">

**크레덴셜 추가**

위 Github Access Token 항목에서 만들어진 토큰을 이용해 젠킨스로 가서 Credentials를 Secret에 넣어 만들어준다.

<img width="538" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/06d6d6c5-2ed0-4eee-a082-ef1946fa22ae">

**Branch Source 추가**

위에서 만든 Credential을 선택해주고 원격 Repository 주소도 적어주고 오른쪽 아래 Validate를 눌러 확인해준다.

<img width="681" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a8b8b75e-b703-41ac-888c-1358695b0aad">

**Build Configuration**

Jenkinsfile을 통해 Pipeline을 구축할 것이기에 아래와 같이 설정한다.

<img width="680" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/d8653bb9-8e4e-4fa6-98b2-7f9f96e573d0">

**Webhook 연결**

Repository에 push가 일어났을 때, Jenkins에게 push가 발생했음을 알려주는 Webhook을 연결해준다.

**Scan Repository Triggers**

<img width="682" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/dccfa00b-7548-4537-b3c2-e6e50dac71a7">

**Jenkinsfile 생성**

Jekinsfile을 이용해 스크립트를 작성해주면 된다.

이벤트가 발생한 브랜치를 체크아웃 해오고 gradle 빌드한다.

결과는 아래 그림과 같다.

```
pipeline {
    agent any

    stages {
        // Checkout Git repository
        stage('Checkout Git') {
            steps {
                checkout scm
                echo 'Git Checkout Success!'
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
//                 sh 'gradle test'
                echo 'Test Success!'
            }
        }
    }

    post {
        success {
            echo 'Build Success'
        }
    }
}
```

<img width="682" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/4dad6181-de20-44af-9a87-ea6aba02e3ca">

### 4. 결론

GithubAction, Jenkins 등등 다양한 CI 도구를 만나 고민을 하였다.

조사 끝에 Jenkins가 GithubAction보다 상대적으로  CI를 구성하기 어렵다고 하였지만, 현업에서 많이 사용하는 만큼 참고자료도 많이 있었기에 Jenkins를 선택하였다.

처음 구성하는 만큼 많은 어려움이 있었고 우여곡절 끝에 CI를 구성할 수 있었다.

다음시간에는 CD구성하여 잘 남겨보도록 하자. 화이팅.