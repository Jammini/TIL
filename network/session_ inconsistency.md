# 세션불일치 현상은 무엇이고 어떤 방법으로 해결할 수 있을까?

### 목차

1. [개요](#1-개요)
2. [Sticky Session](#2-sticky-session)
    
    2-1. [Sticky Session 문제점](#2-1-sticky-session-문제점)
    
3. [Session Clustering](#3-session-clustering)
    
    3-1. [TOMCAT 세션 클러스터링](#3-1-tomcat-세션-클러스터링)
    
    3-2. [Session Clustering 문제점](#3-2-session-clustering-문제점)
    
4. [Session Storage](#4-session-storage)
    
    4-1. [Session Storage 문제점](#4-1-session-storage-문제점)
    
5. [결론](#5-결론)

## 1. 개요

서버의 성능을 업그레이드 하는 경우, 우리는 Scale-Up과 Scale-Out 방법을 생각할 수 있다.

그 중 Scale-Out을 하였을 경우, 세션을 어떻게 공유하여 정합성 이슈를 해결하는지에 대해 알아보자

## 2. Sticky Session

- 번역 그대로 고정된 세션을 의미한다.

<img width="716" alt="image" src="https://user-images.githubusercontent.com/59176149/232076057-dc823cc1-bf43-4404-9fa4-60de175efcb8.png">


1. User1이 로그인 요청을 보낸다
2. 로드밸런서가 서버 1에 User1 요청 정보를 보낸다.
3. 로그인요청을 처리하고 1번 서버에 세션을 생성을 한다.
4. User1이 보낸 모든 요청은 1번 서버로만 보낸다.

즉, 로드 밸런서는 User가 첫 번재 세션을 생성한 서버로 모든 요청하여 고정된 세션만 사용하게 한다.

- 로드 밸런서는 요청을 받으면 가장 먼저 해당 요청에 쿠키가 존재하는지 확인을 한다.
    - 쿠키가 존재하면, 지정된 서버로 전송한다.
    - 쿠키가 존재하지 않다면, 로드 밸런서가 기존 로드 밸런싱 알고리즘을 기반으로 서버를 선정한다.

### 2-1. Sticky Session 문제점

1. 특정 서버에 트래픽이 집중되는 문제

<img width="703" alt="image" src="https://user-images.githubusercontent.com/59176149/232076141-11b9819a-b17d-4960-beb5-929e1ebaa0a0.png">

- 사용자가 접속해야 하는 서버가 정해져 있기 때문에 하나의 서버에 트래픽이 집중되어 있더라도 사용자는 자신의 세션이 없는 다른 서버를 사용할 수 없게된다.

2. 세션 정보의 유실

<img width="720" alt="image" src="https://user-images.githubusercontent.com/59176149/232076251-fb6b9e00-d5c6-4001-b135-4a1f0269af05.png">

- 서비스 중에 만일 하나의 서버에 장애가 발생하게 되면 해당 서버를 사용하는 사용자들은 세션 정보를 모두 잃게 된다.
- 이렇게 되면 다른 서버에서 세션 인증을 다시 해야 하는 문제가 생기게된다

## 3. Session Clustering

- 여러 대의 컴퓨터들이 연결되어 하나의 시스템처럼 동작하도록 만드는 것을 클러스터링이라고 한다.
- 서버들을 하나의 클러스터로 묶어서 클러스터 내의 서버들이 세션을 공유하는 방식

가장 대표적 WAS인 톰캣(Tomcat)은 Seseesion Clustering을 어떻게 구현했는지 살펴보자.

### 3-1. TOMCAT 세션 클러스터링

1. **All-to-All Session Replication**
- 하나의 세션 클러스터 내에서 데이터가 변경되면 변경된 사항이 다른 모든 서버로 복제 되는 방식이다.

<img width="703" alt="image" src="https://user-images.githubusercontent.com/59176149/232076421-ab1d6470-4401-4bc8-a58e-58a3da88bfc9.png">

- 그림과 같이 세션을 복제한다면 유저가 이후에 어떤 서버에 접속하더라도 로그인 정보가 세션에 복제되어 있으므로 정합성 이슈가 해결 가능하다. 이로써 서버 하나에 장애가 발생하더라도 서비스는 중단되지 않고 운영 가능합니다.

1-1. 문제점

1. 많은 메모리 필요
    - 모든 서버가 동일한 세션 객체를 가져야 하기 때문에 많은 메모리가 필요하다.
2. 네트워크 트래픽이 증가
    - 세션을 저장할 때 서버 수 만큼 복제하고 각 서버에 전달, 저장해야하기 때문에 서버 수에 비례하여 네트워크 트래픽이 증가하게 되어 성능 저하가 발생하게 된다.
3. 세션 복제 시간차로 인한 불일치 문제

2. **Primary-Secondary Session Replication**
- Primary 서버의 세션 데이터를 Secondary(Backup) 서버에만 전체 복제하여 저장하는 방식으로, BackupManager 클래스를 통해 이 방식을 제공하고 있다.

<img width="706" alt="image" src="https://user-images.githubusercontent.com/59176149/232076463-a89e7499-be1a-48eb-8201-130cf739a53d.png">

- Primary 서버와 Secondary(Backup) 서버에만 전체 세션을 복제하여 저장하되, 나머지 이외의 서버들에는 세션의 Key에 해당하는 JSESSIONID만 복제, 저장함으로써 메모리를 절약할 수 있는 방식

2-1. 문제점

1. 세션을 복제하는데 걸리는 시간은 줄일 수 있었으나, Primay 서버와 Secondary 서버를 제외한 Proxy 서버에 세션 정보를 요청할 경우에는 다시 Primary 서버에 요청하여 해당 Key에 해당하는 객체를 받아와야만 한다.

### 3-2. Session Clustering 문제점

앞서 설명 했지만 Session Clustering의 문제를 종합 하자면 다음과 같이 발생하게 된다.

1. 서버 세팅의 어려움
2. 추가 메모리 비용
3. 네트워크 트래픽 증가
4. 세션 복제 시간차로 인한 불일치 발생

## 4. Session Storage

- 기존 서버가 가지고 있는 로컬 세션 저장소를 이용하는 것이 아니라, **별도의 세션 저장소**를 사용하는 것을 의미한다.

<img width="716" alt="image" src="https://user-images.githubusercontent.com/59176149/232076653-d55902ce-740e-417b-b9e7-c069656ab429.png">


- 그림과 같이 세션 스토리지가 분리되면, 서버가 늘어나도 세션 스토리지에 대한 정보만 각각의 서버에 입력해주면 세션을 공유할 수 있다.
- 서버가 하나 장애가 발생하더라도 별도의 세션 저장소가 존재하여 서비스를 지속적으로 제공이 가능하다.
- 세션을 저장할 때 세션을 복제해 다른 서버들에 보낼 필요가 없어 WAS들끼리 불필요한 네트워크 통신 과정을 진행하지 않아도 되어 성능면에서도 유리하다.

### 4-1. Session Storage 문제점

1. 문제가 생기면 모든 서버가 장애
    - Session storage 자체에 장애가 발생할 경우 모든 세션을 잃어버려 세션을 사용하는 모든 서버에 영향이 끼치는 위험이 있다.
        - 문제를 보완하기 위해 동일한 세션 저장소를 하나 더 구성하는 방법(마스터-슬레이브 복제)으로 해당 문제를 해결하곤 한다.
2. 성능적인 마이너스
    - 별도의 Session storage로부터 세션을 불러와야 하기 때문에 추가적인 네트워크 I/O가 발생한다.

## 5. 결론

- Sticky Session은 세션을 처음 생성한 서버로 스티커처럼 딱 붙어 고정하는 방식을 사용하여 세션 불일치 문제를 해결한다.
- Session Clustering은 새션을 생성할 때마다 복제하여 서버의 세션 정보를 일치시켜 불일치 문제를 해결한다.
- Session Storagesms 별도의 세션 저장소를 사용하여 세션불일치 문제를 해결하다.

### 참고

- [https://hyuntaeknote.tistory.com/6](https://hyuntaeknote.tistory.com/6)
- [https://inpa.tistory.com/entry/WEB-🌐-세션Session-불일치-문제-해결법-⸢서버-다중화-환경-⸥](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-%EC%84%B8%EC%85%98Session-%EB%B6%88%EC%9D%BC%EC%B9%98-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0%EB%B2%95-%E2%B8%A2%EC%84%9C%EB%B2%84-%EB%8B%A4%EC%A4%91%ED%99%94-%ED%99%98%EA%B2%BD-%E2%B8%A5)
