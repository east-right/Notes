#docker #가상환경 #리눅스
Docker Compose down과 이미지 삭제의 차이
## Docker Compose down vs 이미지 삭제
### 1. docker-compose down
```shell
docker-compose down
```
삭제되는 것들:
- ✅ 컨테이너 (실행 중인 서비스)
- ✅ 네트워크 (컨테이너 간 연결)
- ❌ 이미지 (유지됨)
- ❌ 볼륨 (데이터 유지됨)
의미: 서비스를 중지하고 컨테이너만 삭제. 이미지는 그대로 남아있어서 나중에 다시 실행할 때 빠름

### 2. docker-compose down -v
```shell
docker-compose down -v
```
삭제되는 것들:
- ✅ 컨테이너
- ✅ 네트워크
- ✅ 볼륨 (데이터도 삭제됨!)
- ❌ 이미지 (유지됨)
의미: 컨테이너와 데이터까지 모두 삭제. 이미지는 남아있음

### 3. docker rmi (이미지 삭제)
```shell
docker rmi clickhouse/clickhouse-server:latest
```
삭제되는 것들:
- ❌ 컨테이너 (이미 중지된 것만)
- ❌ 네트워크
- ❌ 볼륨
- ✅ 이미지 (다운로드한 파일)
의미: 이미지 파일 자체를 삭제. 다시 실행하려면 이미지를 다시 다운로드해야 함

## 실제 예시
```shell
# 1. 서비스만 중지 (이미지 유지)
docker-compose down
# → 나중에 docker-compose up 하면 빠름

# 2. 서비스 + 데이터 삭제 (이미지 유지)
docker-compose down -v
# → 나중에 docker-compose up 하면 빠르지만 데이터는 초기화

# 3. 이미지까지 삭제
docker-compose down -v
docker rmi clickhouse/clickhouse-server:latest
# → 나중에 docker-compose up 하면 이미지부터 다시 다운로드
```

## 언제 뭘 사용할까?
- 일시 중지: docker-compose down
- 완전 초기화: docker-compose down -v
- 공간 절약: 이미지까지 삭제
- 개발 중: docker-compose down (빠른 재시작)
