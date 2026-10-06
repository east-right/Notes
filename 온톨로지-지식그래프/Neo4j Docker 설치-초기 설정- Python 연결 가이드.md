# Neo4j Docker 설치/초기 설정 + Python 연결 가이드
## 0. 준비물
- Docker Desktop(Windows/macOS) 또는 Docker Engine(Linux)
- (선택) Python 3.9+ 환경
---
## 1) Neo4j Docker 실행 (가장 기본)
> 웹 UI(7474) + Bolt(7687) 둘 다 열어서 실행합니다.
```bash
docker run -d --name neo4j -p 7474:7474 -p 7687:7687 -e NEO4J_AUTH=neo4j/test1234 neo4j:5
```
- 컨테이너 이름: `neo4j`
- 웹 UI 포트: `7474`
- Bolt 포트: `7687`
- 계정/비번: `neo4j / test1234`
---
## 2) 실행 확인
### 2-1. 컨테이너 상태 확인
```bash
docker ps
```
정상이라면 `Ports`에 아래처럼 보입니다.
- `0.0.0.0:7474->7474/tcp`
- `0.0.0.0:7687->7687/tcp`

### 2-2. 로그 확인(문제 있을 때)
```bash
docker logs neo4j
```
---
## 3) 웹으로 접속 (Neo4j Browser)
브라우저에서 접속:
- [http://localhost:7474](http://localhost:7474/)
로그인:
- Username: `neo4j`
- Password: `test1234`
---
## 4) Python 연결
### 4-1. 드라이버 설치
```bash
pip install neo4j
```

### 4-2. 최소 연결 테스트 코드
```python
from neo4j import GraphDatabase

URI = "bolt://localhost:7687"
USERNAME = "neo4j"
PASSWORD = "test1234"

driver = GraphDatabase.driver(URI, auth=(USERNAME, PASSWORD))

with driver.session() as session:
    result = session.run("RETURN 1 AS test")
    print(result.single()["test"])

driver.close()
```

정상 출력:

```
1
```

---

## 5) 자주 쓰는 운영 명령어
### 5-1. 컨테이너 중지 / 시작 / 재시작
```bash
docker stop neo4j
docker start neo4j
docker restart neo4j
```
### 5-2. 컨테이너 삭제 (데이터도 함께 삭제됨)
```bash
docker rm -f neo4j
```

> ⚠️ `docker rm` 하면 컨테이너 내부 데이터도 같이 날아갈 수 있습니다.  
> 운영/장기 저장 필요하면 아래 “데이터 영구 저장”을 꼭 쓰세요.

---

## 6) (권장) 데이터 영구 저장 옵션 (Volume)
> 컨테이너를 삭제해도 DB 데이터가 유지됩니다.
```bash
docker run -d --name neo4j \
  -p 7474:7474 -p 7687:7687 \
  -e NEO4J_AUTH=neo4j/test1234 \
  -v neo4j_data:/data \
  -v neo4j_logs:/logs \
  neo4j:5
```
- 데이터: `neo4j_data` 볼륨에 저장
- 로그: `neo4j_logs` 볼륨에 저장

---

## 7) 트러블슈팅
### 7-1. `git`처럼 “실행 중 컨테이너에 포트 추가”는 불가
Docker는 **컨테이너 실행 후 포트 추가가 안 됩니다.**  
→ 포트 바꾸려면 `rm` 후 다시 `run` 해야 합니다.
### 7-2. 웹은 되는데 Python이 안 될 때
- Python은 **Bolt(7687)**로 연결해야 합니다.
- URI 확인: `bolt://localhost:7687`

### 7-3. `Authentication failure`
- 비밀번호 오타
- 이전에 다른 비밀번호로 컨테이너 띄운 적 있음  
    → `docker logs neo4j`로 확인 후, 필요하면 컨테이너 삭제하고 다시 실행

---

## 8) 포트 요약
- 웹 UI (HTTP): `7474` → [http://localhost:7474](http://localhost:7474/)
- Bolt (Driver): `7687` → bolt://localhost:7687

---