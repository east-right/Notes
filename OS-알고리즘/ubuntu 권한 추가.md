1. 기본적으로 `sudo`가 가능한 계정이던가  `su` 상태여야함
	1. 나는 `sudo`도 불가능한 상태였어 가지고 su 상태에서 진행함
- ## su로 root 권한을 얻은 상태에서 사용자 추가
```shell
# 현재 root 권한인지 확인
whoami
# dongwoo 사용자를 docker 그룹에 추가
usermod -aG docker dongwoo
# 그룹이 추가되었는지 확인
groups dongwoo
# 또는
id dongwoo
```
- ## Docker 그룹이 존재하는지 확인
```shell
# docker 그룹이 있는지 확인
getent group docker
# 없다면 docker 그룹 생성
groupadd docker
```
- ## 완전한 순서
```shell
# 1. root 권한 확인
whoami

# 2. docker 그룹 생성 (없다면)
groupadd docker

# 3. dongwoo 사용자를 docker 그룹에 추가
usermod -aG docker dongwoo

# 4. 확인
groups dongwoo
```
## 권한 적용을 위한 방법
그룹에 추가한 후에는 다음 중 하나를 해야 함
1. 완전히 로그아웃 후 다시 로그인
2. dongwoo 사용자로 전환: su - dongwoo
3. newgrp 명령어 사용: newgrp docker (dongwoo 사용자로 전환 후)
이렇게 하시면 dongwoo 사용자가 Docker를 사용할 수 있게 됨