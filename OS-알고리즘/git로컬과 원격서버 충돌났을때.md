#git #github 

### 문제 상황
> - github(원격)에서 마무리를 한다고 필요 없는 파일을 몇 개 github에서 삭제한 상태
> - 하지만 다시 확인해보니 수정할 내용이 코드 파일 한 개 수정
> - 그거 깜빡하고 pull안하고 로컬에서 수정하고 push하니깐 충돌난 상태

정리하면 아래와 같다.
***현재 상황***
- **GitHub 원격 저장소**에서는: 몇몇 파일을 삭제함
- **내 로컬**에서는: 그 삭제 사실을 모르고 일부 파일을 수정함
- 하지만 실제로 삭제는 맞는 작업이고, **로컬에서 수정한 파일만 반영하고 싶음**

***목표***
- GitHub 쪽 삭제 내용은 그대로 유지하고
- **내가 수정한 파일만 add + commit + pull** 하고 싶음
- 즉, 삭제된 파일 다시 생기면 안 됨 (충돌도 피하고 싶음)

```bash
# 1. 먼저, 내가 수정한 파일만 스테이징
git add [수정한파일명]

# 2. 커밋
git commit -m "내가 수정한 내용만 커밋"

# 3. 원격 저장소에서 변경 사항을 **리베이스로 깔끔하게 가져오기**
git pull --rebase

#번외: 만약 삭제된 파일까지 git add .으로 스테이징 했으면 아래 코드로 리셋
git reset [삭제된파일명]
```
위 코드와 같이 하니깐 아래와 같은 에러가 뜸
```bash
error: cannot pull with rebase: You have unstaged changes.
error: Please commit or stash them.
```
즉, **아직 git add 안 된 변경사항(unstaged changes)** 이 있어서 rebase 할 수 없다는 뜻
2 개 수정을 하긴 했는데 나머지 한 개는 굳이 올릴필요가 없어서 놔둔건데 이게 발목을 잡음
- Git은 리베이스 전에 **작업 트리가 깨끗해야 함**
##### 방법 ①: 나머지 변경사항을 "스태시(stash)" 해두기
> 지금 변경한 거 잠시 숨겨놓고 rebase 후 다시 꺼내기
```bash
# 이거 전부 내가 올리고 싶은거 commit 한 상태에서 진행을 함
# 1. 변경사항 임시 저장
git stash push -m "임시 저장"

# 2. 깔끔한 상태에서 pull with rebase
git pull --rebase

# 3. stash 꺼내기 (변경사항 다시 적용)
git stash pop
```

##### 방법 ②: 모든 변경사항을 커밋 (수정한 내용이 전부 원하는 거라면)

```bash
git add .
git commit -m "모든 수정사항 커밋"
git pull --rebase
```

단, 삭제된 파일까지 같이 커밋되면 안 되는 상황이라면 이 방법은 ❌
그럴 땐 다시 방법1을 쓰는 걸 추천