# 성능 스키마

부하가 높은 데이터베이스의 성능을 튜닝하는 일은 반복적으로 수행하게 된다.

이러한 데이터베이스를 튜닝과 개선하는 활동을 하면서 여러가지 원인과 이유를 찾는 과정을 거치게 된다.

이러한 여러 가지 질문에 대해서 대답을 찾는 여러가지 방법이 있을 것이며, 원인과 이유 등을 찾기 위한 살펴보는 데이터 중에서 MySQL 에서는 여러 중요한 데이터 중에서 Performance Schema 가 있다.

# 성능 스키마 소개

MySQL서버 내에서 실행되는 작업에 대한 상세한 메트릭을 제공한다.

1. 인스트루먼트
    - 정보를 얻고자 하는 MySQL코드의 어떤 부분을 나타낸다.
        - ex) 메타 데이터 잠금에 대한 정보 수집하려면 wait/lock/metadata/sql/mdl 인스트루먼트를 활성화해야 한다
2. 컨슈머
    - 어떤 코드가 수행되었는지에 대한 정보를 저장하는 단순한 테이블
    - 쿼리를 수행하면 컨슈머는 총 실행횟수, 인덱스가 사용되지 않은 횟수, 수행시간 등과 같은 정보 기록

### 인스트루먼트 요소

performance_schema의 setup_instruments 테이블에는 지원되는 모든 인스트루먼트 목록이 포함되어 있으며, 모든 인스트루먼트 명은 슬래시로 구분해서 구성되어 있다.

- statement/sql/select
- wait/synch/mutex/innodb/autoinc_mutex

가장 왼쪽이 인스트루먼트의 종류를 의미, 따라서 위의 예시에서 statement는 인스트루먼트가 sql 문장 wait/ 은 대기 관련된 내용을 나타냄.

### 컨슈머 체계

 인스트루먼트가 정보를 보내는 대상을 의미한다.  performance_schema는 인스트루먼트 결과를 많은 테이블에 저장한다.

버전 별로 차이가 있지만 MySQL Community 8.0.25 버전에는 performance_schema 에 110개의 테이블이 있다.

### 현재 및 과거 데이터

이벤트는 이름이 다음과 같이 끝나는 테이블에 넣는다.

- _current : 현재 서버에서 발생하고 있는 이벤트
- _history : 스레드당 최종 10개의 완료된 이벤트
- _history_long : 전역으로 스레드당 최종 10,000개의 완료된 이벤트
- _history 와 *_history_long 테이블의 크기는 MySQL 시스템 변수에서 설정할 수 있습니다.

다음과 같은 지표에 대해서 현재와 과거 데이터를 확인 할 수 있다.

- events_wait : 뮤텍스 획득과 같은 로우-레벨 서버 대기
- events_statements : SQL 문
- events_stages : 임시 테이블 생성이나 데이터 전송과 같은 프로파일 정보
- events_transactions : 트랜잭션

### 요약(Summary) 테이블과 다이제스트(digest)

요약 테이블은 테이블이 제안하는 모든 것에 대한 집계된 정보를 저장한다.

다이제스트는 쿼리에서 변경되는 부분(where 절의 상수값 등)을 제거하고 쿼리를 집계하는 방법

### 인스턴스

인스턴스는 MySQL 설치에 사용할 수 있는 객체 인스턴스를 의미

file_instances 테이블에는 파일명과 이들 파일에 대한 액세스한 스레드 수를 포함

### 셋업(Setup)

테이블은 현재 instrumentation 에 대한 설정 정보를 제공하고, 구성을 변경할 수 있도록 지원하며, performance_schema의 런타임 설정에 사용

### 다른 테이블들

테이블 이름에서 네이밍 룰(또는 패턴)을 따르지 않는 테이블이 있습니다. 예를 들어 metadata_locks 테이블은 메타데이터 락에 대한 정보를 제공

### 자원소비

성능스키마에 의해 수집된 데이터는 메모리에 보관된다. 컨슈머의 최대 사이즈를 설정해서 사용하는 메모리량은 제한할 수 있다.

performance_schema의 일부 테이블은 자동 크기 조정을 지원하며, MySQL 인스턴스 시작시에는 최소한의 메모리를 할당하고 필요에 따라서 크기를 조정하게 된다.

다만, 특정 인스트루먼트는 비활성화 하고 테이블을 truncate한 이후에도 할당된 이 메모리가 해제되지 않는다.

모든 인스트루먼트 호출은 performance_schema에 성능 관련 데이터를 저장하기 위해 두개의 매크로를 호출하게 된다. 그래서 더 많은 인스트루먼트를 수행할 수록 CPU 사용량이 증가할 수 있게 된다.

### 제약 사항

- MySQL 컴포넌트에서 지원해야 한다.

메모리 인스트루먼트를 지원하는 않는 스토리지 엔진이면 찾을 수 없다.

- 특정 인스트루먼트와 컨슈머가 활성화된 후에만 데이터를 수집한다.

모든 인스트루먼트가 비활성화된 상태에서 MySQL 서버를 시작한 다음 메모리 사용량을 측정하기로 결정했다면 정확한 양을 알 수 없다.

- 메모리 해제가 어렵다.

MySQL 서버를 시작할 때 컨슈머의 사이즈를 제한하거나 자동 크기 설정으로 둘 수 있다. (체크필요)

자동 크기 설정의 경우 시작 시 메모리를 할당하지 않고 인스트루먼트가 활성화된 데이터를 수집될 때만 메모리를 할당하게 된다.

그러나 이후에 특정 인스트루먼트 또는 컨슈머를 비활성화하더라도 MySQL 서버를 다시 시작하지 않으면 메모리는 해제되지 않는다.

### sys 스키마

버전 5.7부터 표준 MySQL 배포판에는 sys 스키마라고 하는 performance_schema 데이터에 대한 동반 스키마가 포함되어 있다. performance_schema를 보다 손쉽게 사용하도록 설계 및 제공되고 있으며, 자체적으로 데이터를 저장하고 있지는 않다.

### 스레드 이해하기

MySQL 서버는 멀티 스레드 방식으로 동작하는 데이터베이스 소프트웨어이다.

# 설정

performance_schema 자체에 대한 설정과 메모리 사용 및 수집된 데이터에 대한 제한과 관련된 시스템 변수에 대한 활성화 또는 비활성화 하는 것과 같은 부분은 서버 시작시에만 변경할 수 있다.

performance_schema의 인스트루먼트 및 컨슈머의 활성화 또는 비활성화는 동적으로 변경가능하다.

그래서 모든 인스트루먼트를 처음부터 활성화하는 것이 아니라 인스투먼트 별로 시작할 때부터 활성화해서 수집할 내용이 있을 수도 있고, 필요시에 따라서 그때 그때 마다 활성화해서 사용하는 것이 performance_schema에 의한 CPU 및 메모리 리소스 사용을 낭비하지 않는 방법이라고 생각한다.

### 성능스키마의 활성화와 비활성화

performance_schema 자체를 활성화 또는 비활성화하려면 MySQL 서버의 performance_schema 시스템 변수를 ON(1) 또는 OFF(0) 로 설정하면 된다. 해당 시스템 변수는 MySQL 시작(재시작)에만 적용이 가능하다.

### 인스트루먼트 활성화 비활성화

인스트루먼트를 활성화하거나 비활성화를 하는 방법은 3가지가 있다.

- setup_intruments 테이블을 변경 (update)
- sys스키마의 ps_setup_intrument 스토어드 프로시저를 사용
- my.cnf 에서 performance-schema-instrument 파라미터 설정 후 MySQL 서버를 시작

### 컨슈머 활성화와 비활성화

인스트루먼트와 마찬가지로 동일하게 **컨슈머**도 다음 방법을 통해서 활성화 또는 비활성화할 수 있다.

- performance_schema.setup_consumers 테이블을 update
- sys 스키마의 ps_setup_enable_consumer or ps_setup_disable_consumer 를 사용하여 활성/비활성화
- my.cnf 에 performance-schema-consumer 시스템 변수를 설정

### 특정 객체에 대한 모니터링

**setup_objects** 테이블은 performance_schema가 특정 객체 유형 또는 스키마를 모니터링 여부를 제어할 수 있다.

object_type 컬럼은 event, function, procedure, table, trigger 다섯 가지 값 중에 하나의 값을 지정해서 사용한다.

### 스레드 모니터링

setup_threads 테이블에는 모니터링 대상이 되는 백그라운드 스레드 목록이 기본적으로 포함되어 있다.

ENABLED 컬럼은 해당 스레드에 대해서 인스트루먼트의 활성화 여부를 의미한다. HISTORY 컬럼은 계측된 이벤트 정보를 _history 및 _history_long 테이블에도 저장할지 여부를 설정하는 컬럼

### 성능 스키마에 대한 메모리 크기 조정

수집된 데이터는 performance_schema 엔진을 사용하는 테이블에 저장을 하게 되며, 이 엔진은 데이터를 메모리에 저장한다. 일부 performance_schema 테이블은 기본으로 크기 조정이 자동으로 되기도 하며, 일부 테이블은 row의 수가 정해져 있다.

### 기본값

MySQL 시스템 변수 기본값은 버전이 변경됨에 따라 달리질수 있게 된다.

**MySQL 5.7 버전 부터 performance_schema 는 기본적으로 활성화** 되어 있다. 하지만 많은 인스트루먼트가 비활성화 되어 있다.

Global, 스레드, 구문 및 트랜잭션 인스트루먼트만 기본 활성화되어 있다.

**MySQL 8.0 에서부터는 추가로 메타데이터 잠금 및 메모리 인스트루먼트가 기본으로 활성화** 되어 있다.

mysql, information_schema, performance_schema 데이터베이스는 계측(데이터 수집)되지 않는다. 다른 스키마에서는 모든 객체, 스레드 등이 모니터링 및 수집 적재된다.

# 성능 스키마 사용

성능 스키마를 설정하는 방법을 다루었으므로 일반적인 문제 사례를 해결하는 데 도움이 되는 예를 보자

### SQL문 점검

SQL 구문에 관련된 활성화가 필요한 인스트루먼트는 아래와 같다.

- statement/sql : SELECT 또는 CREATE TABLE 과 같은 SQL문
- statement/sp : 스토어드 프로시저
- statement/scheduler : 이벤트 스케줄러
- statement/com : quit,KILL,DROP DATABASE,Binlog Dump 와 같은 명령어, 일부는 mysqld 프로세스 자체에서 호출
- statement/abstract : 네가지 명령 클래스 - clone,Query,new_packet, relay_log

### 일반 SQL문

performance_schema에는 events_statements_current, _history , _history_long 테이블에 구문 메트릭스 저장

event_statement_history항목은 여러개가 있다. 아래 공식 문서 참고

- https://dev.mysql.com/doc/refman/8.0/en/performance-schema-events-statements-current-table.html

### sys 스키마 이용

sys 스키마는 문제가 있는 구문을 찾는데 사용할 수 있는 뷰들을 제공한다.

예를 들어 statements_with_errors_or_warnings 은 오류와 경고를 반환한 모드 구문에 대한 정보를 나열하고 statements_with_full_table_scans는 전체 테이블 스캔이 필요한 모든 구문을 나열한다.

sys스키마에는 쿼리 텍스트 대신에 다이제스트(DIGEST)가 들어가 있다.

performance_schema에서 DIGEST을 통해서 DIGEST SQL 텍스트를 확인 할 수 있다.

최적화가 필요한 구문을 찾는데 도움이 될 수 있는 SYS스키마의 뷰는 다음과 같이 있다.

- statements_with_errors_or_warnings
- statements_with_full_table_scans
- statements_with_runtimes_in_95th_percentile
- statements_with_sorting
- statements_with_temp_tables

### 프리페어드 스테이트먼트

prepared_statements_instances 테이블은 서버에 존재하는 모든 프리페어드 스테이트먼트를 포함한다.

events_statements_[current|histroy|history_long] 테이블과 동일한 통계를 가지며, 추가로 프리페어드 스테이트먼트를 소유한 스레드와 구문이 실행된 횟수에 대한 정보를 가지고 있다.

events_statements_[current|history|histroy_long] 테이블과 달리 통계 데이터가 합산되어 테이블은 모든 구문 실행의 총합을 포합한다.

프리페어드 스테이트먼트 계측을 활성화하는 인스트루먼트는 다음과 같다.

- statement/sql/prepare_sql : 텍스트 프로토콜의 PREPARE 문(MySQL CLI통해 실행할 때)
- statement/sql/execute_sql : 텍스트 프로토콜의 EXECUTE 문(MySQL CLI통해 실행할 때)
- statement/com/Prepare : 바이너리 프로토콜의 PREPARE 문(MySQL C API를 통해 액세스 하는 경우)
- statement/com/Execute : 바이너리 프로토콜의 EXECUTE 문(MySQL C API를 통해 액세스 하는 경우)

### 스토어드 루틴

performance_schema를 사용해서 스토어드 루틴이 어떻게 실행되었는지에 대한 정보를 검색할 수 있다.

예를 들어 IF ... ELSE 흐름 제어 분기가 선택되었는지 또는 에러 핸들러가 호출이 되었는지와 같은 정보를 확인할 수 있다.

스토어드 루틴의 계측을 활성화하기 위해서는 'statement/sp/%' 패턴의 인스트루먼트를 활성화해야 한다.

statement/sp/stmt 인스트루먼트는 루틴 내에서 호출되는 구문을 담당하는 반면, 다른 인스트루먼트는 프로시저, 루프 또는 기타 제어 명령을 시작하거나 나가는 것과 같은 이벤트 추적을 담당

### 구문 프로파일링

events_stages_[current|history|history_long] 테이블에는 MySQL이 임시 테이블을 생성하거나 업데이트하거나 잠금을 기다리는 동안 소요 시간과 같은 정보가 포함되어 있다.

프로파일링을 활성화 하려면 해당 컨슈머와 'stage/%' 패턴의 인스트루먼트를 활성화해야한다.

### 읽기 대 쓰기 성능 점검

performance_schema 구문 계측은 실행된 워크로드가 읽기 작업인지, 쓰기 작업인지, 또는 어떤 유형의 작업이 많고 적었는지를 이해하고 파악하는데 많은 도움과 유용하다.

events_statements 에 있는 event_name 별로 건수 등을 아래와 같이 조회함으로써 데이터베이스 상에서의 워크로드의 비중을 파악할 수 있다.

```sql
mysql> select event_name,count(event_name)
from events_statements_history_long
group by event_name;
+-----------------------------+-------------------+
| event_name                  | count(event_name) |
+-----------------------------+-------------------+
| statement/sql/insert        |               504 |
| statement/sql/delete        |               502 |
| statement/sql/select        |              6787 |
| statement/sql/update        |              1007 |
| statement/sql/commit        |               500 |
| statement/sql/begin         |               500 |
+-----------------------------+-------------------+
```

위와  같이 구문의 대기 시간을 알고 싶으면 LOCK_TIME 열로 집계가 가능하다.

```sql
mysql> select event_name,count(event_name),
SUM(LOCK_TIME/1000000) AS latency_ms
from events_statements_history
group by event_name order by latency_ms DESC;
```

### 메타데이터 잠금 점검

메타데이터 잠금은 데이터베이스 객체 정의가 변경되지 않도록 보호하는 데 사용 한다.

select , update 등의 모든 SQL 문에 대해 공유 메타데이터 잠금이 설정 된다.

공유 메타데이터 잠금이 요구되는 다른 SQL명령문에는 영향을 미치지 않는다.

그러나 ALTER TABLE 또는 CREATE INDEX 와 같이 데이터베이스 객체 정의를 변경하는 명령문들은 잠금이 해제 될 때까지 시작하는 것을 막아준다.

잠금을 기다리는 구문은 일반적으로 명확합니다. DDL 명령문은 암시적으로 트랜잭션을 커밋하므로 그것은 새로운 트랜잭션 구문이 되게 되며 프로세스 목록의 상태에서 "**Waiting for a metadata lock**" 으로 확인 된다.

### 메모리 사용량 점검

performance_schema에서 메모리 계측을 켜려면 클래스 memory의 인스트루먼트를 활성화해라.

활성화하면 MySQL 구조 내부에서 정확히 메모리를 어떻게 사용하는지에 대한 세부 정보를 확인할 수 있다.

### 자주 발생하는 오류 점검

특정 오류 정보뿐 아니라 performance_schema는 추가적으로 사용자, 호스트 계정, 스레드 및 전역 오류 번호별로 오류를 집계하는 다이제스트 테이블을 제공한다.

모든 집계 테이블은 events_errors_summary_global_by_error 테이블에서 사용한 것과 유사한 구조를 가진다.

```sql
mysql> select *
from performance_schema.events_errors_summary_global_by_error
where sum_error_raised > 10 and user is not null
order by sum_error_raised desc;
```

오류 다이제스트 테이블은 가장 잘못된 쿼리를 전송하고 작업을 수행한 사용자계정, 호스트 사용자 스레드를 찾는데 유용하다.

### 성능 스키마 자체 점검

일반 스키마에 대해 수행한 것과 동일한 인스트루먼트와 컨슈머를 사용해서 성능 스키마 자체를 점검할 수 있다.

performance_schema가 기본 데이터베이스로 설정된 경우에는 이에 대한 쿼리를 추척하지 않는다.

performance_schema에 대한 쿼리 점검해야 하는 경우 setup_actors테이블을 업데이트 해야한다.

### 요약

성능스키마는 예전에는 잘 사용안했으나 성능 스키마가 자체 메모리를 관리하는 방법을 이해해야 MySQL이 메모리를 누출하지 않도록 관리할 수 있다.

컨슈머 데이터를 메모리에 유지하고 다시 시작할 때만 해당 메모리를 해제하는게 좋다.

성능 스키마를 활성하 하고 쿼리 성능, 잠금, 디스크 I/O, 오류 등 고민하는 문제를 해결하는데 도움이 되는 인스트루먼트와 컨슈머를 동적으로 활성화 해라.
