# MySQL 아키텍쳐

### 목차

1. [MySQL논리적 아키텍처](#1-mysql논리적-아키텍처)
2. [커넥션 핸들러](#2-커넥션-핸들러)
3. [SQL Interface](#3-sql-interface)
4. [Paser](#4-parser)
5. [Optimizer](#5-optimizer)
6. [Cache & Buffer](#6-cache--buffer)

## 1. MySQL논리적 아키텍처

MySQL 서버는 크게 MySQL엔진과 스토리지 엔진으로 구분된다.

<img width="703" alt="image" src="https://github.com/Jammini/TIL/assets/59176149/fe68178f-327e-43fd-9993-8d18d82e31f3">


- 클라이언트
    - MySQL에 고유한 서비스보다는 대부분의 네트워크 기반 클라이언트/서버 도구 또는 서버에 필요한 연결처리, 인증, 보안 등의 서비스가 포함.
- MySQL 엔진
    - 쿼리 파싱, 분석, 최적화 및 모든 기본 제공 함수를 포함하여 MySQL에서 대부분의 지능적인 부분이 속함.
- 스토리지엔진
    - MySQL에 저장된 모든 데이터를 저장하고 검색하는 역할 담당. 단순히 서버의 요청에 응답.

### 2. 커넥션 핸들러

Connection Pool은 MySQL Architecutre의 최상위 계층으로 Client의 Connection을 생성 및 관리하며 요청 Query를 처리한다.

**연결처리**

MySQL Server는 Client의 Connection 요청에 대해 Thread를 할당한다. Client는 할당받은 Thread에서 Query를 수행한다.

(Thread는 Server에 의해 캐시 되므로 새로운 Connection에 대하여 항상 생성할 필요는 없다.)

**인증**

MySQL Server는 Client가 연결될 때마다 Client의 호스트, 사용자 이름, 패스워드 등을 기반으로 인증을 수행한다.

**보안**

MySQL Server에 Client가 성공적으로 연결되면 Server는 해당 Client가 특정 Query를 수행할 권한이 있는지 확인한다.

### 3. SQL Interface

MySQL은 Command를 수신하고 Client에게 결과를 전송하는 Interface로 ANSI SQL 표준을 준수하며, 대부분의 ANSI 호환 Database Server의 SQL을 Interface로 사용한다.

SQL Interface 구성요소는 DML, DDL, Stored Procedures, Views, Triggers 등이 있다.

### 4. Parser

사용자 요청 Query를 토큰(MySQL이 인식할 수 있는 최소 단위의 어휘나 기호)으로 분리, Tree 형태의 구조로 만들어 내는 작업을 의미한다. 이 과정에서 Query의 문법 오류 발견 시 사용자에게 오류 메시지를 전달한다.

### 5. Optimizer

사용자의 요청 Query를 얼마나 낮은 비용으로 효율적으로 처리할지를 결정하는 역할을 수행한다. Query 재작성, 스캔 순서 조정 및 인덱스의 선택과 같은 작업을 수행한다.

### 6. Cache & Buffer

Data 및 인덱스에 대해 빠르게 Read/Write 하기 위한 목적으로 사용되는 메모리 공간이다. MyISAM의 Key Cache나 InnoDB의 Buffer Pool과 같은 보조 저장소를 이야기한다.

8.0 릴리스 부터는 쿼리 캐시가 완전히 사라졌다. 하지만 자주 제공되는 결과 셋은 캐싱하는 것이 좋으며 널리 사용되는 디자인 패턴은 memcached 또는 Redis로 데이터를 캐시한다.
