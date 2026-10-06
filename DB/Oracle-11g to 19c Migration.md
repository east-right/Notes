# 1] Docker로 Oracle 11g-xe, 19c-xe 설치

[https://soir1984.tistory.com/84](https://soir1984.tistory.com/84)

## Oracle 11g-xe

1. 공개된 오라클 이미지 확인
    1. `docker search oracle-xe-11g`
    2. 추천 수가 가장 많은 이미지 선택해서 다운로드: `docker pull oracleinanutshell/oracle-xe-11g`
    3. 다운로드 받은 이미지 실행: `docker run -d -p 1521:1521 oracleinanutshell/oracle-xe-11g`
    4. 정상적으로 실행되었는지 확인: `docker ps`
2. Oracle Shell 접속
    1. `docker exec -it [CONTAINER_NAME] bash`
    2. `sqlplus` 명령어로 SQL 접속
    3. 초기 관리자 정보: `system/oracle`
3. Oracle 외부 접속
    1. SQL Developer Tool 다운로드([https://www.oracle.com/kr/database/sqldeveloper/](https://www.oracle.com/kr/database/sqldeveloper/)) ⇒ VSCode Extension에서 다운로드하여 대체
    2. New Connection을 생성하여 “사용자 이름, 비밀번호, 호스트 이름, 포트(1521), SID=`xe`" 설정하여 저장 및 접속
4. Oracle 계정 생성 및 권한 추가
    1. 새로운 `.sql` 파일 생성
    2. 계정 생성: `CREATE USER <user> IDENTIFIED BY <passwd> DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;`
    3. 권한 부여: `GRANT CONNECT, RESOURCE TO <user>;`
    4. 설정 저장: `COMMIT;`
5. `sysdba` 권한으로 접속할거면 가장 간단하게
	1. `su - oracle`로 리눅스 사용자 변경
	2. `sqlplus / as sysdba` 로 접속
	3. 바로 dba권한으로 접속 가능

---

## Oracle-19c

[https://velog.io/@aryumka/TIL-Docker로-oracle-19c-세팅하기](https://velog.io/@aryumka/TIL-Docker%EB%A1%9C-oracle-19c-%EC%84%B8%ED%8C%85%ED%95%98%EA%B8%B0)

- docker container 실행: `docker run -d --name oracle-19c-xe -p 1521:1521 -e ORACLE_SID=ORCL -e ORACLE_PWD=oracle -e ORACLE_CHARACTERSET=KO16MSWIN949 -v C:/Users/InDBLine/Desktop/oracle-test/oracle-19c/oradata/:/opt/oracle/oradata doctorkirk/oracle-19c`
- `docker exec -it oracle-19c-xe bash` 명령어로 bash 진입
- `sqlplus / as sysdba` 명령어로 SQL 실행 확인