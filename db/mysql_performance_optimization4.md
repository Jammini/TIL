# 4장 운영 체제 및 하드웨어 최적화

# 개요

MySQL에 필요한 4가지 기본 리소스는 CPU, 메모리, 디스크, 네트워크 리소스이다. 네트워크는 심각한 병목 현상을 거의 나타내지 않지만, CPU, 메모리, 디스크는 확실히 자주 나타난다

따라서, 하드웨어를 신중하게 선택하고 운영체제를 적절하게 구성해라.

# MySQL의 성능을 제한하는 요소

가장 흔한 병목 현상은 CPU 고갈이다. MySQL이 병렬로 많은 쿼리를 실행하려고 하거나 CPU에서 쿼리가 너무 오랫동안 실행될 때 발생할 수 있다. 

I/O 포화도 발생할 수 있지만 CPU 고갈이 훨씬 많다. CPU 고갈은 주로 SSD 사용으로의 전환으로 발생한다. 과거 작업을 메모리에서 하지 않고 HDD 에서 수행할 때는 특히나 성능 저하가 극심했다.

### MySQL용 CPU를 선택하는 방법

현재 하드웨어를 업그레이드하거나 새 하드웨어를 구입할 때 워크로드가 CPU에 종속되는지 여부를 고려해야 한다. 

CPU사용률을 확인하여 CPU 바운드 워크로드를 식별할 수도 있지만 CPU 전체 부하량만 보는 대신 가장 중요한 쿼리에 대한 CPU 사용률과 I/O의 균형을 살펴보고 CPU가 고르게 로드 되는지 확인한다.

일반적으로 아래 2가지 목표가 있다.

1. 짧은 대기 시간(빠른 응답 시간)
    - 각 쿼리가 하나의 CPU만 사용하기 떄문에 빠른 CPU가 필요하다
2. 높은 처리량
    - 동시에 여러 쿼리를 실행하고자 하면, 멀티 CPU의 이점을 통해 쿼리 처리.

따라서, 현재 하드웨어를 업그레이드하거나 새로 구매해야한다면, 워크로드 CPU에 종속되는지 여부를 고려해야 한다. CPU 전체 부하량만 보는 대신, 가장 중요한 쿼리에 대한 CPU 사용률과 I/O 균형을 살펴보고 CPU가 고르게 로드 되었는지 확인해야한다.

워크로드가 모든 CPU를 활용하지 않는다면, MySQL은 InnoDB버퍼 삭제, 네트워크 작업 등과 같은 백그라운드 작업에 추가 CPU를 사용해라. 다만, 이런 작업들은 일반적으로 쿼리를 실행하는 것에 비해 미미한 수준이다.

# 메모리 및 디스크 리소스의 균형 유지

MySQL이 메모리를 사용하는 주된 이유는 결국 성능에 있다. 궁극적으로 느린 디스크 I/O 대신 메모리에 있는 데이터에 엑세스하는 것이 성능 상에서 더 많은 이점을 지닌 것이다

따라서, 메모리와 디스크 크기, 속도, 비용 및 기타 품질의 균형을 유지하여 워크로드 성능을 향상시키는 것이 중요하다.

### 읽기, 쓰기, 캐싱

모든 데이터가 메모리에 있는 경우, 서버의 캐시가 준비되면 모든 읽기가 캐시 히트 하게된다. 메모리에서 논리적인 읽기는 계속되지만 디스크에서 물리적인 읽기는 없다. 

읽기와 마찬가지로 메모리에서 쓰기를 수행할 수 있지만, 빨리 디스크에 기록해서 영구적이 되게 해야한다.

즉, 캐시는 쓰기를 지연시킬 수 있지만, 읽기처럼 쓰기를 배제할 수 없다.

쓰기 지연을 허용하는 것 외에 캐싱을 사용하면 쓰기를 두 가지 중요한 방식으로 그룹화할 수 있다.

1. 다수의 쓰기, 한 번의 플러시
    - 모든 새 값을 디스크에 기록하지 않고도 단일 데이터 조각을 메모리에서 여러 번 변경할 수 있다.
    - 데이터가 디스크로 플러시되면 마지막 물리적 쓰기 이후 발생한 모든 수정사항은 영구적이다
2. I/O 병합
    - 메모리에서 다양한 데이터 조각을 수정할 수 있고 수정 사항을 함께 수집할 수 있으므로 단일 디스크 작업으로 물리적 쓰기를 수행할 수 있다.

많은 트랜잭션 시스템이 로그 선행 기법 전략을 사용하는 이유이다. 디스크에 대한 변경 내용을 플러시하지 않고 메모리에 있는 페이지를 변경할 수 있다.

쓰기는 랜덤I/O를 보다 순차적인 I/O로 변환하기 때문에 버퍼링 이점이 크다. 비동기(버퍼링된) 쓰기는 일반적으로 운영체제에서 처리되며, 디스크에 보다 효율적으로 플러시할 수 있도록 일괄 처리 된다.

### 작업 세트란?

실제로 작업을 수행하는데 필요한 데이터

# 솔리드 스테이트 스토리지

솔리드 스테이트 ( 플래시 ) 저장 장치는 자기 플래터 대신 셀로 구성된 비휘발성 플래시 메모리 칩 ( NonVolatile Random Access Memory )을 사용한다. 움직이는 부품이 없어 하드 드라이브와 매우 다르게 작동한다.

대부분의 데이터베이스 시스템, 특히 OLTP(온라인 트랜잭션 처리)의 표준이다.

솔리드 스테이트 저장 장치는 자기 플래터 대신 셀로 구성된 비휘발성 플래시 메모리 칩을 사용한다. NVRAM(NonVolatile Random Access Memory)이라고도 한다.

1. 하드 드라이브에 비해 훨씬 우수한 랜덤 읽기 및 쓰기 성능
    - 플래시 장치는 일반적으로 쓰기보다 읽기에서 약간 더 우수하다.
2. 하드 드라이브보다 우수한 순차 읽기 및 쓰기 성능
    - 순차I/O에 비해 랜덤 I/O에서 속도가 훨씬 느리므로 랜덤 I/O보다 크게 향상되지는 않는다.
3. 하드 드라이브보다 월등하게 나은 동시성 지원
    - 플래시 장치는 더 많은 동시 작업을 지원할 수 있다.

플래시 메모리는 높은 동시성으로 매우 우수한 랜덤I/O성능을 제공한다.

### 플래시 메모리 개요

플래시 메모리의 가장 중요한 특징은 여러 번 빠르게 작은 단위로 읽을 수 있지만 쓰기는 훨씬 더 어렵다는 것이다.

쓰기 작업이 잘 수행되고 플래시 메모리 블록이 너무 일찍 마모되는 것을 방지하려면 장치가 페이지를 재배치하고 가비지 컬렉션, 이른바 웨어 레벨링을 수행할 수 있어야 한다.

쓰기증폭 : 부분블록  쓰기로 인해 데이터를 이곳저곳 이동하거나 데이터 및 메타데이터를 여러번 기록함으로써 발생하는 추가 쓰기를 설명하는데 사용된다.

### 가비지 컬렉션

일부 블록을 최신 상태로 유지하고 새로운 쓰기를 준비하기 위해 장치는 블록을 회수한다.

이를 위해 장치는 약간의 여유공간이 필요한데, 장치 내부에 보이지 않는 일부 예약된 공간이 있거나 완전히 채우지 않도록 직접 공간을 예약해야 한다.

장치가 가득차면 가비지 컬렉션이 일부 블록을 깨끗하게 유지하기 위해 열심히 일해야 하므로 쓰기 증폭 계수 ( write amplification factor ) 가 증가한다.

```sql
쓰기 증폭 계수란?

부분 블록 쓰기로 인해 데이터를 이곳저곳 이동하거나 데이터 및 메타데이터를 여러 번 기록하면서 발생하는 추가 쓰기이다.
```

# RAID 성능 최적화

스토리지 엔진은 종종 데이터 및/또는 인덱스를 하나의 대용량 파일에 보관한다. 

즉, RAID는 일반적으로 많은 데이터를 저장하는데 가장 적합한 옵션임을 뜻한다.

RAID는 이중화 스토리지 크기, 캐싱 및 속도에 도움이 된다. 하지만 RAID 구성에도 다양한 변형이 있으므로 요구사항에 맞는 것을 선택하는것이 중요하다.

| 레벨 | 개요 | 중복 | 필요디스크 | 읽기 가속 | 쓰기 가속 |
| --- | --- | --- | --- | --- | --- |
| RAID 0 | 저렴함, 빠름, 위험함 | 불가 | N | 가능 | 가능 |
| RAID 1 | 빠른 읽기, 간편함, 안전함 | 가능 | 2(통상적) | 가능 | 불가 |
| RAID 5 | SSD로 저렴하고 빠름 | 가능 | N+1 | 가능 | 상황에 따라 다름 |
| RAID 6 | RAID 5와 유사하지만 복원력이 더 뛰어남 | 가능 | N+2 | 가능 | 상황에 따하 다름 |
| RAID 10 | 고가, 빠름, 안전함 | 가능 | 2N | 가능 | 가능 |
| RAID 50 | 매우 큰 데이터 저장소 목적 | 가능 | 2(N+1) | 가능 | 가능 |

### RAID 장애, 복구 및 모니터링

RAID 0을 제외한 RAID 구성은 이중화를 제공한다. 동시 디스크 장애 발생 가능성을 간과하면 안된다. 즉, RAID가 데이터 안전을 보장한다고 생각해서는 안된다.

따라서 RAID어레이를 모니터링하는 것이 중요하다. 대부분 컨트롤러는 어레이 상태를 보고하기 위한 소프트웨어를 제공한다. 그렇지 않으면 드라이브 장애를 전혀 알 수 없기에 계속 추척해야 한다.

드라이브 장애 모니터링 외에도 RAID컨트롤러의 배터리 백업 장치와 쓰기 캐시 정책을 모니터링해야 한다.

배터리에 장애가 발생하면 기본적으로 대부분의 컨트롤러는 라이트백 캐시(write-back cache) 대신 동시쓰기(write-through cache)로 캐시 정책을 변경하여 쓰기 캐싱을 비활성화 한다. 이로 인해 성능이 크게 저하 될 수 있다.

### RAID 구성 및 캐싱

일반적으로 시스템의 부팅과정에서 설정 유틸리티를 통해 입력하거나 명령 프롬프트에서 실행하여 RAID 컨트롤러 자체를 구성할 수 있다.

대부분 컨트롤러는 많은 옵션을 제공하지만 중점을 두는 두가지는 아래와 같다.

1. RAID 스트라이프 청크 크기
    - 이상적인 청크 크기는 워크로드 및 하드웨어 따라 다르다.
    - 랜덤 I/O의 경우 청크 크기가 큰 것이 좋다. → 단일 드라이브에서 더 많은 읽기를 수행할 수 있기 때문
    - 그렇다고 읽기 단위가 너무 크면 캐시 효율성이 떨어 질수 있다. → 작은 요청에도 실제 필요한 것보다 훨씬 많은 데이터를 읽어 오기 때문
2. RAID 캐시(온 컨트롤러 캐시)
    - 하드웨어 RAID 컨트롤러에 물리적으로 설치된 (상대적으로) 적은 양의 메모리이다.
    - 디스크와 호스트 시스템 사이를 이동할 때 데이터를 버퍼링하는데 사용할 수 있다.
    
    RAID 카드가 캐시를 사용할 수 있는 몇가지 이유는 아래와 같다.
    
    1. 캐싱 읽기
    2. 미리 읽기 데이터 캐싱
    3. 쓰기 캐싱
    4. 내부 작업
    
    RAID 컨틀로러의 메모리는 현명하게 사용해야 하는 희소 리소스이다. 읽기용으로 사용하는 것은 일반적으로 낭비지만, 쓰기에 사용하는 것은 I/O 성능을 향상시키는 중요한 방법이다.
    

# 네트워크 설정

지연시간과 대역폭은 네트워크 연결의 제한 요소이다. 대부분의 애플리케이션에서 가장 큰 문제는 대기 시간이다.

일반적인 조정 중 하나는 로컬 포트 범위를 변경하는 것이다.

하지만, 일반적으로 네트워크 성능이 극도로 낮거나 연결 수가 매우 많은 경우와 같은 비정상적인 상황이 발생하는 경우에만 변경해야 한다.

# 파일 시스템 선택하기

파일시스템은 운영체제에 따라 크게 다르다. 윈도우는 실제로 한 두가지 옵션만 선택할 수 있고 실제로 사용할 수 있는 NTFS는 한가지뿐이다. 반면 GNU/Linux는 많은 파일시스템을 지원한다.

대게는 ext4, XFS 또는 ZFS와 같은 저널링 파일시스템을 사용하는 것이 가장 좋다. 그렇지 않으면 충돌 후 파일시스템을 확인하는데 시간이 오래 걸릴 수 있다.

ext3 또는 그 후속 버전인 ext4를 사용하는 경우 데이터 저널링 방법에 대한 세가지 옵션이 있다.

1. data=writeback
2. data=ordered
3. data=journal

데이터베이스용 파일시스템을 고려할 때는 사용가능기간, 사용기간 및 운영 환경에서 어느 정도 검증되었는지 고려하는 것이 좋다. 파일시스템 비트는 데이터베이스에 있는 가장 낮은 수준의 데이터 무결성이다.

### 디스크 큐 스케줄러 선택

GNU/Linux에서 큐 스케줄러는 블록 장치에 대한 요청이 실제로 기본 장치로 전송되는 순서를 결정한다.

기본값은 cfg(Completely Fair Queing)이다.

MySQL이 생성하는 워크로드 유형에서 큐에서 일부 요청을 불필요하게 지연시키기 때문에 응답 시간이 매우 느려진다.

noop 스케줄러는 하드웨어 RAID 컨트롤러 및 SAN(저장 영역 네트워크)과 같이 백그라운드에서 자체 스케줄링을 수행하는 장치에 적합하며 데드라인은 RAID컨트롤러와 직접 연결된 디스크 모두에 적합하다.

심각한 성능 문제를 일으킬 수 있는 cfg 이외에서의 사용이 중요하다.

### 메모리와 스와핑

MySQL은 많은 양의 메모리가 할당되어 있을 때 가장 잘 작동한다.

InnoDB는 디스크 액세스를 방지하기 위에 메모리를 캐시로 사용한다. 즉, 메모리 시스템의 성능이 쿼리 제공 속도에 직접적인 영향을 미칠 수 있다.

오늘날 더 빠른 메모리 액세스를 보장하는 좋은 방법 중 하나는 내장 메모리 할당자(glibc)를 tcmalloc 또는 jemalloc과 같은 외부 할당자로 교체하는 것

→ 수 많은 벤치마크에서 이 두가지 모두 glibc에 비해 성능이 향상되고 메모리 조각화가 감소하는 것으로 나타담.

스와핑은 운영체제에 가상 메모리를 저장할 물리적 메모리가 충분하지 않기 때문에 일부 가상 메모리를 디스크에 쓸 때 발생한다.

스와핑은 운영체제에서 실행중인 프로세스에만 적용된다.

디스크의 전체 수명을 단축 시킬 수 있는 불필요한 쓰기를 방지하기 위해서라도 스와핑을 적극적으로 피해야 한다.

### 운영 시스템 상태

운영 체제는 운영 체제와 하드웨어가 수행하는 작업을 찾는 데 도움이 되는 도구를 제공한다.

널리 사용되는 두가지 도구는 아래와 같다.

1. iostat
2. vmstat

두가지를 도구를 사용하면 구간 인수를 지정할 수 있다. 그러면 서버가 어떤 작업을 하고 있는지 보여주는 보다 유의미한 증분 보고서를 생성할 수 있다.

### 그 외 유용한 도구들

vmstat는 일반적으로 많은 유닉스 계역 운영체제에 기본적으로 설치된다.

dstat, collectl, mpstat 같은 도구도 있다.

디스크 I/O 사용향을 검토할때 blktrace가 유용하다.

pt-diststats, pref 등등…

### 요약

MySQL에 맞게 하드웨어를 선택하고 구성하는 것과 하드웨어에 맞게 MySQL을 구성하는 것은 신비로운게 아니다.

성능과 비용사이에서 적절한 균형을 찾아라.
