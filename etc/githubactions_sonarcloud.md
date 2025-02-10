# Github Actions으로 Sonar Cloud와 Slack 알림 설정하기

### 목차

1. [개요](#1-개요)
2. [시나리오 소개](#2-시나리오-소개)
3. [사전 작업 구성](#3-사전-작업-구성)
4. [워크플로우 구성](#4-워크플로우-구성)
5. [마치며](#5-마치며)

### 1. 개요

Github Actions를 이용하여 CI/CD 파이프라인 구성에 대한 이해를 해보려고 한다.

Github Actions는 Github 레포지토리를 이용해 간단하게 설정하고 사용하기 쉽다. 별도의 서버나 외부서비스를 구축할 필요 없이 Github에서 제공하는 기능으로 CI를 실행할 수 있기에 Github Actions를 선택하였다.

### 2. 시나리오 소개

**요구사항**

1. 특정 path 에 대해서만 실행 (multi-board에 src/**)
2. main branch로 PR이 생성 & 동기화 될 때 테스트 작업 실행 (CI)
3. PR이 main으로 merge 되면 배포 (CD)
4. 배포 성공 여부 슬랙으로 전송

위에 요구사항을 반영한 대략적인 플로우는 다음과 같다.

![Image](https://github.com/user-attachments/assets/fbdaad7a-f80e-41b6-9323-1433f4dfc29b)

1. main branch에서 분기된 feature branch 에서 commit 을 하고 업데이트 합니다.
2. src/** 내에 파일이 변경되거나 업데이트 한다.
3. feature branch가 main branch로 PR을 만들면 CI를 수행한다.
4. CI 프로세스가 성공한 다음 배포를 위한 PR을 merge 하면 개발 환경에 배포한다.

### 3. 사전 작업 구성

**<워크 플로우 권한 범위 변경>**

![Image](https://github.com/user-attachments/assets/95260f07-e327-4485-8296-107fee3babf3)

Repository에 Settings에 들어가 Actions > General 탭에 들어간다.

![Image](https://github.com/user-attachments/assets/b7cf77d9-b271-401f-a905-b08c450732f4)

Workflow permissions을 Read and write permissions으로 변경한다.

**<Github Personal Access Token 생성>**

![Image](https://github.com/user-attachments/assets/01066dee-8b9b-4e10-8c33-23749ca60763)

Settings > Developer Settings > Personal access tokens > Tokens (classic)

![Image](https://github.com/user-attachments/assets/55a4a345-3930-4fd2-8986-e441b2f89017)

![Image](https://github.com/user-attachments/assets/71177fe5-bf0e-4da0-a496-d534e1d61086)

Note에 해당 토큰 네임을 작성해주고 Expiration 과 해당하는 Select scopes를 선택해서 생성해준다.

![Image](https://github.com/user-attachments/assets/cda1ffef-bca3-4ae5-b016-1676292e8ed8)

생성하면 다음과 같이 secret 값이 나오는데 이 값을 따로 저장해두면 된다.(다시 확인할 수 없기 때문에 꼭 저장해주자.)

이후에 Github actions 가 사용할 Github 권한을 이 Token을 이용해서 받아오게 된다.

**<Slack Webhook>**

Slack을 사용하는 이유는 Github actions이 Slack으로 메세지를 보내기 위해 아래와 같은 과정을 해준다.

https://api.slack.com/messaging/webhooks

해당 페이지를 들어가서 확인해보자.

![Image](https://github.com/user-attachments/assets/97b1eff3-9220-4c8e-b995-eb9b18700527)

Your Apps 에 들어가 Create New App 을 클릭한다.

![Image](https://github.com/user-attachments/assets/aa3b7f98-1875-469c-98bc-880643ecead0)

다음과 같은 화면이 나오는데 From a manifest 클릭

![Image](https://github.com/user-attachments/assets/4745f85b-a30c-4cbc-9692-48ad25d90301)

해당 워크스페이스 설정하고 Next 클릭

![Image](https://github.com/user-attachments/assets/21c225d3-ed65-4f2c-8484-68240a4bfe46)

Features > Incoming Webhooks 에 들어가 활성화 시켜준다.

![Image](https://github.com/user-attachments/assets/e2cc6d3c-8147-46c3-ab46-caf3457bfbd8)

메세지를 받을 채널을 다음과 같이 만들어준다.

![Image](https://github.com/user-attachments/assets/42170357-1ded-4ea6-bc24-98610e50e9fe)

![Image](https://github.com/user-attachments/assets/70e0a147-3d60-4371-8182-460c318e97bc)

![Image](https://github.com/user-attachments/assets/1d6c353a-4cf0-47ad-a46b-1253678a3f65)

![Image](https://github.com/user-attachments/assets/6b9eeece-dd5e-405b-89fd-fab403a952a3)

Add New Webhook to Workspace 를 클릭하여 방금 만든 워크스페이스를 액세스 하기 위한 권한을 설정해준다.

![Image](https://github.com/user-attachments/assets/f5987e5d-f7d0-46e3-9832-555e2649ac85)

![Image](https://github.com/user-attachments/assets/a41e83a2-db18-4f46-872c-b1063c92c5de)

Allow를 클릭하면 모든 사전 과정이 완료된다.

![Image](https://github.com/user-attachments/assets/05abae4a-3149-45b3-87cc-c9e0697911ca)

테스트를 위해 다음과 같은 내용을 복사해서 command에 넣어 사용해준다.

![Image](https://github.com/user-attachments/assets/39d16c1c-f152-4ad0-8b5c-5fe3dd39507d)

OK를 확인하고 슬랙 해당 채널에 Hello World 메세지가 출력된 것을 확인할 수 있다.

![Image](https://github.com/user-attachments/assets/bf49d4b8-b7b9-4cae-b3ff-d10044f3d5ce)

**<SonarCloud와 Github Repository 연동하기>**

[SonarCloud](https://www.sonarsource.com/products/sonarcloud/) 로그인

![Image](https://github.com/user-attachments/assets/d558d279-875a-47b0-a1f4-87758d7de6e7)

Analyze new project 클릭

![Image](https://github.com/user-attachments/assets/25d0a4fd-6021-46c3-afe4-0bb01ae0d449)

![Image](https://github.com/user-attachments/assets/1c996358-9403-4443-bf09-7da2065753d3)

Select repositories 를 통해 해당 프로젝트를 추가하고 Save 해준다.

![Image](https://github.com/user-attachments/assets/643df55a-3622-49f3-b515-97d60c5b7f74)

해당 프로젝트 선택후 Set up 클릭.

![Image](https://github.com/user-attachments/assets/0711c77f-00b8-426e-b417-9ff39a0054e0)

Previous version 선택 후 Create project 클릭

![Image](https://github.com/user-attachments/assets/d3409398-376d-46bb-aaa3-0f122fc0d225)

프로젝트가 생성되면 현재 소스를 분석해서 보여준다.

![Image](https://github.com/user-attachments/assets/270cc4eb-8482-4bfe-9198-4a47af858f86)

Automatic Analysis 끄기

### 4. 워크플로우 구성

Github Actions workflow를 작성해 CI 작업 SonarCloud 연동, 배포 성공여부 슬랙 전송을 구성해보자.

```yaml
name: CI/CD Pipeline

on:
  pull_request:
    types: [opened, synchronize, closed]
    branches: [main]
    paths:
      - 'src/**'
  push:
    branches:
      - main

jobs:
  # CI & SonarCloud Analysis
  sonarcloud:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'zulu'
          cache: 'gradle'

      - name: Cache SonarCloud packages
        uses: actions/cache@v4
        with:
          path: ~/.sonar/cache
          key: ${{ runner.os }}-sonar
          restore-keys: ${{ runner.os }}-sonar

      - name: Cache Gradle packages
        uses: actions/cache@v4
        with:
          path: ~/.gradle/caches
          key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*') }}
          restore-keys: ${{ runner.os }}-gradle

      - name: Build & SonarCloud Analysis
        id: build
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
        run: ./gradlew build sonar --info

      - name: Notify Build Status
        if: always()
        uses: slackapi/slack-github-action@v2.0.0
        with:
          webhook: ${{ secrets.SLACK_WEBHOOK_URL }}
          webhook-type: incoming-webhook
          payload: |
            {
              "text": "SonarCloud Build Status: *${{ job.status }}* for PR #${{ github.event.number }} on branch `${{ github.ref_name }}`."
            }

  # Deployment job after merge
  deploy:
    if: github.event_name == 'push'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'zulu'
          cache: 'gradle'

      - name: Cache Gradle packages
        uses: actions/cache@v4
        with:
          path: ~/.gradle/caches
          key: ${{ runner.os }}-gradle-${{ hashFiles('**/*.gradle*') }}
          restore-keys: ${{ runner.os }}-gradle

      - name: Build for Deployment
        run: ./gradlew build --info

      - name: Deploy Application
        id: deploy
        env:
          DEPLOY_ENV: production
        run: |
          # Add your deployment script here
          echo "Deploying application to $DEPLOY_ENV"
          # Example: ./deploy.sh --env $DEPLOY_ENV

      - name: Notify Deployment Status
        if: always()
        uses: slackapi/slack-github-action@v2.0.0
        with:
          webhook: ${{ secrets.SLACK_WEBHOOK_URL }}
          webhook-type: incoming-webhook
          payload: |
            {
              "text": "Deployment Status: *${{ job.status }}* for branch `${{ github.ref_name }}`."
            }

```

현재 job 을 크게 sonarcloud와  deploy로 나누었지만, CI & SonarCloud Analysis만 이용하여 sonarcloud job만 사용해서 진행중이다.

만약, 추가 job을 이용해 aws나 docker와 같이 이용하면 해당 내용을 알맞게 수정해주면 된다.

sonarcloud job이 잘 수행 되었는지 확인해보자.

![Image](https://github.com/user-attachments/assets/cf0b711b-020c-45c4-ad1b-e3f61000424c)

main branch에서 분기된 multi-board-13 branch가 commit 을 하고 업데이트 하여 PR을 날렸다.

![Image](https://github.com/user-attachments/assets/175c2907-f6fd-40d4-92af-fa8a4a118dca)

PR이 생성되면서 해당 sonarcloud 잡이 잘 동작하는 확인.

![Image](https://github.com/user-attachments/assets/829165d0-403b-454d-801d-4efacb905401)

main branch로 PR이 열렸을때 sonar cloud 에 코멘트가 달리는지 확인.

![Image](https://github.com/user-attachments/assets/0954ed67-39f1-4354-8399-a298424812d0)

Slack 채널에 Build 성공 여부 메세지 출력 확인.

### 5. 마치며

Github actions을 통해 sonar cloud와 slack을 연동하고 빌드의 성공 여부를 자동으로 Slack 알림을 받을 수 있게 되었다.

Gihub actions을 이용하면 다양한 자동화 작업을 수행할 수 있는데 더 많은 활용 방법을 찾아서 하나씩 해봐야 겠다.
