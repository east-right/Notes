#DBA #oracle 
# 시스템 관련
```SQL
SELECT DISTINCT OWNER
FROM DBA_TABLES
```
- DB의 TABLE OWNER 확인

# 테이블 관련
`SELECT * FROM DBA_TABLES WHERE OWNER = :OWNER`
- OWNER 별로 **테이블 통계와 정보**를 추출하는 쿼리
- 아래는 주로 보는 컬럼들 목록
- **`SELECT * FROM DBA_TAB_COMMENTS;` 와 함께 사용하여 큰 서비스에선 각 테이블의 COMMENT까지 같이 수집한다 보통**
- `DBA_TAB_STATISTICS`를 통해서 조금 더 정확하게 + 파티션 통계까지 한번에 볼 수 있다.

| 컬럼              | 의미                   | 비고                                                                      |
| --------------- | -------------------- | ----------------------------------------------------------------------- |
| OWNER           | 테이블 소유자              |                                                                         |
| TABLE_NAME      | 테이블 이름               |                                                                         |
| TABLESPACE_NAME | 테이블 스페이스 이름          | 테이블 스페이스 분석(DISK IO 볼때)                                                 |
| NUM_ROWS        | 테이블 행 수              | 실제가 아닌 통계 기반임                                                           |
| CLUSTER_NAME    | 클러스명                 | 클러스터 테이블이면 표시(DISK IO 분석시 사용)                                           |
| BLOCKS          | 사용 중인 블록 수           |                                                                         |
| PARTITIONED     | 파티션 여부               | YSE OR NO                                                               |
| LOGGING         | Redo 로그 생성 여부        |                                                                         |
| COMPRESSION     | 테이블 압축 여부            |                                                                         |
| DEGREE          | 병렬 DEGREE            | 병렬 처리(PARALLEL)시 자동으로 해당 값 만큼 처리(DEGREEE 8 -> PARALLEL 8)               |
| STATUS          | 메타 데이터 관점에서 객체가 정상인지 | 보통 VALID나옴 TABLE에선 크게 중요하진 않음<br>VIEW나 PROCEDURE에선 매우 중요 INDEX에서도 조금 중요 |
| LAST_ANALYZED   | 마지막 통계 수집 시점         |                                                                         |
| SAMPLE_SIZE     | 통계 수집시 사용한 샘플 수      |                                                                         |

`SELECT * FROM DBA_SEGMENTS WHERE OWNER = :OWENR`
- TABLE의 논리적인 정보를 알려주는게 tables라면 segment는 **물리적인 테이블 정보**를 알려준다.
- tables에는 테이블이 존재하지만 그 테이블이 비어있는 경우에는 segments에는 등장하지 않는다.
- 또한 임시테이블이나 외부 테이블고 세그먼트가 존재하지 않는다.
- 그래서 주로 tables와 합쳐서 존재는 하는데 데이터가 들어 있지 않은 테이블을 찾을 때 유용하다.

`SELECT * FROM DBA_PART_TABLES`
- **파티션 테이블인지** / **파티션 방식(RANGE/HASH/LIST/INTERVAL 등)** / 파티션 키 컬럼 등 설계 정보
- 파티션 키 컬럼 개수, 서브파티션 사용 여부 등 정보를 알 수 있음

| 컬럼                     | 의미              | 비고                                                     |
| ---------------------- | --------------- | ------------------------------------------------------ |
| OWNER                  | 테이블 소유자         |                                                        |
| TABLE_NAME             | 테이블 이름          |                                                        |
| PARTITIONING_TYPE      | 파티션 방식          | 상위 파티션 기준(RANGE/LIST/HASH)                             |
| SUBPARTITIONING_TYPE   | 서브파티션 방식        | 복합 파티션일 때 개중 하위 파티션의 타입을 나타냄<br>(RANGE/LIST/HASH/NULL) |
| PARTITION_COUNT        | 파티션 개수          |                                                        |
| PARTITIONING_KEY_COUNT | 파티션 키 컬럼 개수     | 상위 파티션의 기준으로 파티션을 나누는 컬럼이 몇개 인지 나타냄<br>(어떤 컬럼이 파티션 키x) |
| INTERVAL               | 인터벌 파티션 여부      |                                                        |
| DEF_TABLESPACE_NAME    | 기본 파티션 테이블 스페이스 |                                                        |
| STATUS                 | 테이블 상태          | VALID/UNVALID                                          |
`SELECT * FROM DBA_CONSTRAINTS`
- **테이블의 제약 조건** PK, UK,FK, Not_null 등을 가장 확실히 알 수 있는 딕셔너리
- 그리고 모든 제약 조건 관련 정보를 알 수 있어서 제약 MIN, MAXㅡ NOT_NULL 등과 FK일때 추가 부모 테이블의 제약 정보도 알 수 있다.

| 컬럼               | 의미     | 비고                                                                                                                                          |
| ---------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------- |
| OWNER            | 스키마    |                                                                                                                                             |
| CONSTRAINT_NAME  | 제약명    | PK, UK, FK, NN 등과 같은 정보 EX `PRODUCT_DESCRIPTIONS_PK` 이렇게 나옴<br>NN은 NOT_NULL의 약자로 해당 컬럼이 NULL이 없어야 한다는 제약조건(근데 딱히 필요 없는거 같은데), MAX나 MIN도 있는듯 |
| CONSTRAINT_TYPE  | 제약 타입  | P = PK, C = Custom, R = 뭔가 흐름상 FK인듯                                                                                                         |
| TABLE_NAME       | 테이블 이름 | 만약 2 이상인 값이면 그 컬럼은 특정 제약 조건의 후속 컬럼이다.                                                                                                       |
| SEARCH_CONDITION | 상세제약   | 예시: `"CUSTOMER_ID" IS NOT NULL` ,<br>`product_status in ('orderable','planned','under development','obsolete') `                            |

# 컬럼 관련
`SELECT * FROM DBA_TAB_COLUMNS`
- 각 **테이블의 컬럼 정보**를 알 수 있다. 
- `DBA_COL_COMMENTS` 로 컬럼의 comment를 찾을 수 있다.

| 컬럼             | 의미         | 비고                                                                      |
| -------------- | ---------- | ----------------------------------------------------------------------- |
| OWNER          | 스키마        |                                                                         |
| TABLE_NAME     | 테이블명       |                                                                         |
| COLUMN_NAME    | 컬럼명        |                                                                         |
| DATA_TYPE      | 데이터 타입     |                                                                         |
| DATA_LENGTH    | 길이         | 데이터 저장 길이(byte)<br>ex) CHAR(3), VARCHAR2(10), NUMBER, DATE              |
| DATA_PRECISION | 정밀도        | DATA가 숫자 계열일 때 정수 몇번째 까지 표시하는지<br>ex) NUMBER(5,2) => DATA_PRECISION = 5 |
| DATA_SCALE     | 스케일        | DATA가 숫자 계열일 때 소수 몇번째 까지 표시하는 지<br>ex) NUMBER(5,2) => DATA_SCALE = 2    |
| NULLABLE       | NULL 가능 여부 | 결측 가능 여부                                                                |
| COLUMN_ID      | 컬럼 순서      |                                                                         |
| DATA_DEFAULT   | 기본값        | 디폴트값 말 그대로                                                              |
| HIDDEN_COLUMN  | 내부 컬럼 여부   | 12c 이후 중요한 값                                                            |
`SELECT * FROM DBA_CONS_COLUMNS`
- 테이블에서 각 **컬럼의 제약조건 탐색**
- PK인기 UNIQUE_KEY인지 유추는 가능하지만 조금 불편 

| 컬럼              | 의미   | 비고                                                                                                                                          |
| --------------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| OWNER           | 스키마  |                                                                                                                                             |
| TABLE_NAME      | 테이블명 |                                                                                                                                             |
| COLUMN_NAME     | 컬럼명  |                                                                                                                                             |
| POSITION        | 순서   | 만약 2 이상인 값이면 그 컬럼은 특정 제약 조건의 후속 컬럼이다.                                                                                                       |
| CONSTRAINT_NAME | 제약명  | PK, UK, FK, NN 등과 같은 정보 EX `PRODUCT_DESCRIPTIONS_PK` 이렇게 나옴<br>NN은 NOT_NULL의 약자로 해당 컬럼이 NULL이 없어야 한다는 제약조건(근데 딱히 필요 없는거 같은데), MAX나 MIN도 있는듯 |
`SELECT * FROM DBA_TAB_COL_STATISTICS`
- 그 초기 튜닝의 핵심이 되는 딕셔너리
- 각 PLAN의 핵심이 되는 컬럼의 선택도와 히스토그램이 존재

| 컬럼            | 의미      | 비고                               |
| ------------- | ------- | -------------------------------- |
| NUM_DISTINCT  | NDV     | 그 값의 종류 인듯, 남녀 = 2, 초등학년 = 6 이렇게 |
| DENSITY       | 선택도     |                                  |
| NUM_NULLS     | NULL 개수 |                                  |
| HISTOGRAM     | 히스토그램   |                                  |
| LOW_VALUE     | 최소값     |                                  |
| HIGH_VALUE    | 최대값     |                                  |
| LAST_ANALYZED | 통계 시점   |                                  |
`SELECT * FROM dba_part_key_columns WHERE OWNER = :OWNER`
- 각 **파티션의 키 컬럼**을 알 수 있다.
- 주로 단독으로 저장하지 않고 DBA_PART_TABLES와 합친 다음 저장한다.
> 서브 파티션은 `DBA_SUBPART_KEY_COLUMNS`로 확인 가능하다.

| 컬럼              | 의미         | 비고                    |
| --------------- | ---------- | --------------------- |
| OWNER           | 테이블 소유자    |                       |
| NAME            | 테이블명       |                       |
| COLUMN_NAM      | 파티션 키 컬럼   | 해당 딕셔너리의 가장 핵심이 되는 부분 |
| COLUMN_POSITION | 파티션 키 내 순서 |                       |
`SELECT * FROM DBA_IND_COLUMNS`
- 각 **인덱스에 어떠한  컬럼이 들어있는 지 알 수 있**는 딕셔너리 조회 쿼리이다.

| 컬럼              | 의미         | 비고                                 |
| --------------- | ---------- | ---------------------------------- |
| INDEX_OWNER     | 인덱스 소유자    |                                    |
| INDEX_NAME      | 인덱스명       |                                    |
| TABLE_OWNER     | 테이블 소유자    |                                    |
| TABLE_NAME      | 테이블명       |                                    |
| COLUMN_NAME     | 인덱스 컬럼     |                                    |
| COLUMN_POSITION | 컬럼 순서      | 만약 컬럼 순서의 값이 2다? 해당 인덱스는 결합인덱스 이다. |
| DESCEND         | ASC / DESC | 오름차순, 내림차순                         |

# 인덱스 관련
`SELECT * FROM DBA_INDEXES WHERE OWNER = :OWNER`
- **인덱스 기본 정보**를 추출가능한 딕셔너리이다.

| 컬럼                | 의미                              | 비고        |
| ----------------- | ------------------------------- | --------- |
| OWNER             | 인덱스 소유자                         | 스키마 필터    |
| INDEX_NAME        | 인덱스명                            | 인덱스 식별    |
| TABLE_OWNER       | 테이블 소유자                         | 대상 테이블    |
| TABLE_NAME        | 테이블명                            |           |
| INDEX_TYPE        | BTREE / BITMAP / FUNCTION-BASED | 인덱스 유형    |
| UNIQUENESS        | UNIQUE / NONUNIQUE              | PK/UK 여부  |
| STATUS            | VALID / UNUSABLE                | 장애/재빌드 판단 |
| BLEVEL            | B-Tree 깊이                       | 깊으면 성능 저하 |
| LEAF_BLOCKS       | 리프 블록 수                         | 크기/성능     |
| NUM_ROWS          | 인덱스 행 수                         | 통계 정확성    |
| DISTINCT_KEYS     | NDV                             | 선택도       |
| CLUSTERING_FACTOR | 군집도                             | 인덱스 효율 핵심 |
| LAST_ANALYZED     | 통계 시점                           | 최신 여부     |
| PARTITIONED       | 파티션 여부                          | YES/NO    |

`SELECT * FROM DBA_IND_COLUMNS WHERE INDEX_OWNER = :OWNER`
- 인덱스에 어떤 컬럼이, 어떤 순서로 들어가 있는지 확인 가능하다.
- 즉 **인덱스 컬럼 구성에 대한 정보를 추출 가능하다.**

| 컬럼              | 의미            | 비고                                |
| --------------- | ------------- | --------------------------------- |
| INDEX_OWNER     | 인덱스 소유자       |                                   |
| INDEX_NAME      | 인덱스명          |                                   |
| TABLE_OWNER     | 테이블 소유자       |                                   |
| TABLE_NAME      | 테이블명          |                                   |
| COLUMN_NAME     | 컬럼명           |                                   |
| COLUMN_POSITION | 컬럼 순서 (선두 중요) | 약 컬럼 순서의 값이 2다? 해당 인덱스는 결합인덱스 이다. |
| DESCEND         | ASC / DESC    | 오름차순, 내림차순                        |
`SELECT * FROM DBA_PART_INDEXES`
- **인덱스 파티션 방식**에 대해 조회가 가능하다.

| 컬럼                   | 의미         | 비고                  |
| -------------------- | ---------- | ------------------- |
| INDEX_NAME           | 인덱스명       |                     |
| PARTITIONING_TYPE    | 인덱스 타입     | RANGE / HASH / LIST |
| LOCALITY             | 파티션 인덱스 타입 | LOCAL / GLOBAL      |
| SUBPARTITIONING_TYPE | 서브파티션 방식   |                     |
`SELECT * FROM DBA_IND_PARTITIONS`
- **인덱스 파티션 단위 정보**를 알 수 있다.

| 컬럼             | 의미                | 비고  |
| -------------- | ----------------- | --- |
| PARTITION_NAME | 파티션명              |     |
| STATUS         | USABLE / UNUSABLE |     |
| NUM_ROWS       | 파티션 행 수           |     |
| BLEVEL         | 깊이                |     |
`SELECT * FROM DBA_IND_STATISTICS`
- 인덱스 통계 정보만 따로 추출해서 확인 가능

| 컬럼                | 의미        | 비고  |
| ----------------- | --------- | --- |
| NUM_ROWS          | 행 수       |     |
| DISTINCT_KEYS     | NDV       |     |
| BLEVEL            | 깊이        |     |
| LEAF_BLOCKS       | 리프 블록     |     |
| CLUSTERING_FACTOR | 군집도       |     |
| STALE_STATS       | 통계 오래됨 여부 |     |