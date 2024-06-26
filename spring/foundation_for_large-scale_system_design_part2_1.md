# 1장 근접성 서비스

## 1단계: 문제 이해 및 설계 범위 확정

### 기능 요구사항

- 사용자의 위치(경도와 위도 쌍)와 검색 반경 정보에 매치되는 사업장 목록을 반환
- 사업장 소유주가 사업장 정보를 추가, 삭제, 갱신할 수 있도록 하되, 그 정보가 검색 결과에 실시간으로 반영될 필요는 없다고 가정
- 고객은 사업장의 상세 정보를 살필 수 있어야함

### 비기능 요구사항

- 낮은 응답 지연(latency) : 사용자는 주변 사업장을 신속히 검색할 수 있어야 한다.
- 데이터 보호 : 사용자 위치는 민감한 정보이기에 보호할 방법을 고려해야 한다.
- 고가용성 및 규모 확장성 요구사항 : 트래픽이 급증해도 감당할 수 있도록 시스템을 설계해야 한다.

### 개약적 규모 추정

일간 능동 사용자는 1억명이며 등록된 사업장 수는 2억이라 하자.

## 2단계: 개략적 설계안 제시 및 동의 구하기

1. API 설계
2. 데이터 모델
3. 개략적 설계안
4. 주변 사업장 검색 알고리즘

### 1. API 설계

특정 검색 기준에 맞는 사업잘 목록을 반환한다.

GET /v1/search/nearby

호출인자는 다음과 같다

<img width="458" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/2e616c37-0ee7-4b41-b4fb-2f400ca7312a">
반환되는 결과는 아래와 같은 형태를 띤다

```json
{
	"total": 10,
	"businesses":[{business object}]
}
```

**사업장 관련 API**

<img width="332" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/613b0bd3-3702-4a1d-a856-7366d59fa270">

### 2. 데이터 모델

**읽기/쓰기 비율**

- 주변사업장 검색
- 사업장 정보 확인

쓰기 연산 실행 빈도는 낮고 읽기 연산은 굉장히 자주 일어나는 시스템에는 MySQL 같은 관계형 데이터베이스가 바람직할 수 있다.

**데이터 스키마**

- business 테이블

사업장 상세 정보를 담는다.

<img width="148" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/54ddd5bf-579c-47e6-9d33-f5d1e5b4231c">

- 지리적 위치 색인 테이블

위치 정보 관련 연산의 효율성을 높이는데 쓰인다.

### 3. 개략적 설계

위치 기반 서비스(location-based service, LBS)와 사업장 관련 서비스 두 부분으로 구성된다.

<img width="424" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/7ada6e5a-4e23-4664-a229-065296229032">

**로드밸런서**

유입 트래픽을 자동으로 여러 서비스에 분산시키는 컴포넌트. 로드밸런서를 사용하는 회사는 로드밸런서에 단일 DNS 진입점(entry point)을 지정하고, URL 경로를 분석하여 어느 서비스에 트래픽을 전달하지 결정한다.

**위치 기반 서비스(LBS)**

LBS는 시스템의 핵심 부분으로, 주어진 위치와 반경 정보를 이용해 주변 사업장을 검색한다.

- 쓰기 요청이 없는, 읽기 요청만 빈번하게 발생하는 서비스이다.
- QPS가 높다. 특히 특정 시간대의 인구 밀집 지역일수록 그 경향이 심하다.
- 무상태(stateless) 서비스이므로 수평적 규모 확장이 쉽다.

**사업장 서비스**

- 사업장 소유주가 사업장 정보를 생성, 갱신, 삭제한다. 기본적으로 쓰기 요청이며 QPS가 높지 않다.
- 고객이 사업장 정보를 조회한다. 특정 시간대에 QPS가 높아진다.

**데이터베이스 클러스터**

데이터베이스 클러스터는 주-부(primary-secondary) 데이터베이스 형태로 구성.

주 데이터베이스는 쓰기 요청을 처리, 부 데이터베이스는 읽기 요청 처리.

데이터는 주 데이터베이스에 기록된 다음 사본 데이터베이스로 복사됨.

복재로 인한 시간 지연이 있을 수 있으나 실시간으로 갱신될 필요가 없기 때문에 문제가 되지 않음.

**사업장 서비스와 LBS 규모 확장성**

둘 다 무상태 서비스이므로 점심시간 등의 특정 시간대에 집중적으로 몰리는 트래픽에는 자동으로 서버를 추가하여 대응하고 야간 등 유휴 시간 대에는 서버를 삭제하도록 구성할 수 있다.

클라우드에 두어 가용성을 높일 수도 있다.

### 4. 주변 사업장 검색 알고리즘

많은 회사가 레디스 지오해시나 PostGIS확장을 설치한 포스트그레스 데이터베이스를 활용한다.

**방안 1: 2차원 검색**

주어진 반경으로 그린 원 안에 놓인 사업장을 검색하는 방법.

가장 직관적이지만 지나치게 단순하다는 문제가 있음.

<img width="281" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/a7ce05bb-cfd6-43e8-aa4f-f28c879ddeb3">

<img width="490" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/de3e2be8-0146-40f2-b4fe-836b225567bf">

이 두 집합의 교집합을 구해야 하는데 각 집합에 속한 데이터의 양 때문에 효율적이지 않다.

이 방안에 문제는 데이터베이스 색인으로는 오직 한 차원의 검색속도만 개선된다.

그렇다면 2차원 데이터를 한 차원에 대응시킬 방법이 있다.

그 전에 지리적 정보에 색인을 만드는 방법을 보면 크게 두 종류이다.

<img width="550" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/400b9d54-a255-45f3-b2e0-79d883235c4d">

각 색인법의 구현 방법은 다르지만 지도를 작은 영역으로 분할하고 고속 검색이 가능하도록 색인을 만드는 개략적 아이디어는 같다. 이 가운데 지오해시, 쿼드트리, 구글 S2는 실제로 가장 널리 사용되는 방안이다.

**방안 2: 균등 격자**

지도를 작은 격자 또는 구획으로 나누는 단순한 접근법

하나의 격자는 여러 사업장을 담을 수 있고, 하나의 사업장은 오직 한 격자 안에만 속하게 된다.

<img width="523" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/32de4a1c-f974-40f7-ba03-e6fc3097ce56">

사업장 분포가 균등하지 않다는 문제가 존재. 뉴욕 다운타운에 사업장이 많지만 바다 한가운데는 사업장이 없다.

즉, 동일한 크기의 격자로 나누면 데이터 분포는 전혀 균등하지 않다.

**방안 3: 지오해지(Geohash)**

지오해시는 2차원의 위도 경도 데이터를 1차원의 문자열로 변환한다.

비트를 하나씩 늘려가면서 재귀적으로 세계를 더 작은 격자로 분할해 나간다. 전세계를 자오선과 적도 기준 사분면으로 나눔.

- 위도 범위 [-90, 0]은 0에 대응
- 위도 범위 [0, 90]은 1에 대응
- 경도 범위 [-180, 0]은 0에 대응
- 경도 범위 [0,180]은 1에 대응

<img width="369" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/6c0ce2a5-b122-4ac0-b675-c05d679d6552">

각각의 격자를 또다시 사분면으로 나눈다. 

<img width="389" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/58c5607a-17e7-46e0-b705-bc741f38b978">

원하는 정밀도를 얻을때까지 반복해서 분할한다.

- 격자 가장자리 관련 이슈

공통 접두어가 긴 격자들이 서로 더 가깝게 놓이도록 보장하지만 두 지점이 적도의 다른 쪽에 놓이거나, 자오선상의 가른 반쪽에 놓이는 경우는 이를 보장하지 않는 이슈가 있다.

또한 두 지점이 공통 접두어 길이는 길지만 서로 다른 격자에 놓이는 경우가 있다.

**방안 4: 쿼드트리**

격자의 내용이 특정 기준을 만족할 때까지 2차원 공간을 재귀적으로 사분면 분할하는 데 흔히 사용되는 자료 구조다. 예를 들어 격자에 담긴 사업장 수가 100이하가 될 때까지 분할하는 것이다.

<img width="506" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/0b7648b4-9c4c-4ae5-bf69-d7915b15eceb">

쿼드트리를 구축하는 데는 꽤나 소요되므로 서버를 시작하는 순간에 트리를 구축하면 서버 시간이 길어져 운영상 고려해야할 문제이다. 쿼드트리를 만들고 있는 동안 서버는 트래픽을 처리할 수 없기 때문이다. 또한, 사업장이 추가/삭제되었을때 쿼드트리의 갱신하는 문제도 존재한다.

**방안 5: 구글 S2**

구글 S2 기하 라이브러리는 아주 유명한 솔루션이다.

임의 지역에 다양한 수준의 영역 지정이 가능하다. 스쿨 존이나 동네 경계처럼 이미 존재하는 경계선들을 묶어 설정할 수도 있다.

## 3단계: 상세 설계

1. 데이터베이스 규모 확장
2. 캐시
3. 지역 및 가용성 구역
4. 시간대 또는 사업장 유형에 따른 검색
5. 최종 아키텍처 다이어그램

### 1. 데이터베이스 규모 확장성

**사업장 테이블**

한 서버에 데이터를 담을 수 없을 수도 있다. 따라서 샤딩을 적용하기 좋은 후보다. 이 테이블을 샤딩하는 가장 간단한 방법은 사업장 ID를 기준으로 하는것이다. 모든 샤드에 부하를 고르게 분산할 수 있을 뿐 아니라 운영적 측면에서 보면 관리하기도 쉽다.

**지리 정보 색인 테이블**

지오해시나 쿼드트리 둘 다 널리 사용되지만 단순한 지오해시를 사용한다.

지오 해시 테이블 구성방법은 두가지다.

방안 1: 각각의 지오해시에 연결되는 모든 사업장 ID를 JSON 배열로 만들어 같은 열에 저장하는 방안. 특정한 지오해시에 속한 모든 사업장 ID가 한 열에 보관된다.

방안 2: 같은 지오해시에 속한 사업장 ID 각각을 별도 열로 저장하는 방안

추천 : 두번째 방안

방안 1의 경우, 사업자 정보를 갱신하려면 일단 JSON 배열을 읽은 다음 갱신할 사업장 ID를 찾아내야 한다. 병렬적으로 실행되는 갱신 연산결과로 데이터 소실 방지를 위한 락을 사용해야한다.

방안 2의 경우에는 지오해시와 사업장 ID 칼럼을 합친(geohash, business_id)를 복합 키(compound key)로 사용하면 사업장 정보를 추가하고 삭제하기가 쉽다. 락을 사용할 필요가 없다.

**지리 정보 색인의 규모 확장**

이번 설계안에서는 읽기 부하를 나눌 사본 데이터베이스 서버를 두는 방법이 좋다. 개발도 쉽고 관리도 간편하다.

### 2. 캐시

- 처리 부하가 읽기 중심이고 데이터베이스 크기는 상대적으로 작아서 모든 데이터는 한 대 데이터베이스 서버에 수용 가능하다.
- 읽기 성능이 병목이라면 사본 데이터베이스를 증설해서 읽기 대역폭을 늘릴수 있다.

캐시 도입을 할때는 벤치마킹과 비용분석에 주의해 확신이 들면 캐시 전략 논의를 해라.

### 3. 지역 및 가용성 구역

- 사용자와 시스템 사이의 물리적 거리를 최소한으로 줄일 수 있다. 미국 서부사용자는 해당지역 데이터센터로 연결될 것이고, 유럽 사용자는 유럽데이터센터로 연결될 것이다.
- 트래픽을 인구에 따라 고르게 분산하는 유연성을 확보할 수 있다. 일본과 한국 같은 지역은 인구 밀도가 아주 높다. 별도 지역으로 뺴거나, 아예 한 지역 안에서도 여러 가용성 구역을 활용하여 부하를 분산시키는 것이 바람직할 수 있다.
- 지역의 사생활 보호법에 맞는 운영이 가능하다. 어떤 국가는 사용자 데이터를 해당 국가 이외의 지역으로 전송하지 못하도록 한다.

### 4. 시간대, 혹은 사업장 유형별 검색

만약 지금 영업중인 사업장, 혹은 식당 정보만 받아오고 싶다면 어떻게 해야 할까?

지오해시나 쿼드트리 같은 메커니즘을 통해 전 세계를 작은 격자들로 분할하면 검색 결과로 얻어지는 사업장 수는 상대적으로 적다. 일단 근처 사업장 ID부터 전부 확보한 다음 그 사업장 정보를 전부 추출해서 영업시간이나 사업자 유형에 따라 필터링 한다.

### 5. 최종 설계도

<img width="532" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/876c74fa-9359-4984-86dd-16c2b33edaa0">

**주변 사업장 검색**

1. 옐프에서 주변 반경 500미터 내 모든 식당을 찾는 경우를 생각해보자. 우선 클라이언트 앱은 사용자의 위치(위도, 경도)와 검색반경(500m)을 로드밸런서로 전송한다.
2. 로드밸런서는 해당 요청을 LBS로 보낸다.
3. 주언진 사용자 위치와 반경 정보에 의거하여 LBS는 검색 요건을 만족할 지오해시 길이를 계산한다.
4. LBS는 인접한 지오해시를 계산한 다음 목록에 추가한다. list_of_geohashes = []
5. list_of_geohashes 내에 있는 지오 해시 각각에 대해 LBS는 ‘지오해시’ 레디스 서버를 호출하여 해당 지오해시에 대응하는 모든 사업장 ID를 추출한다.
6. 반환된 사업장 ID들을 가지고 ‘사업장 정보’ 레디스 서버를 조회하여 각 사업장의 상세 정보를 취득한다. 해당 상세 정보에 의거하여 사업장과 사용자 간 거리를 확실하게 계산하고, 우선순위를 매긴 다음 클라이언트 앱에 반환한다.

**사업장 정보 조회, 갱신, 추가 그리고 삭제**

모든 사업장 정보 관련 API는 LBS와 분리되어 있다.

우선 해당 데이터가 ‘사업장 정보’ 레디스 서버에 기록 되어 있는지 살피고 캐시 되어 있는 경우에 데이터를 읽어 클라이언트에 반환한다. 없는 경우에는 데이터베이스 클러스터에서 사업장 정보를 읽어 캐시에 저장한 다음 반환한다.

## 4단계: 마무리

- 2차원 검색
- 균등 분할 격자
- 지오해시
- 쿼드트리
- 구글 S2

위와 같은 몇가지 색인 방안을 살펴보았는데 IT기업에서 널리 쓰이는 기술은 지오해시, 쿼드트리, S2이다.

<img width="461" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/4dfeac87-f6f2-4b55-b307-0b1926402298">