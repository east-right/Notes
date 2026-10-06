---
title: "[Git] 머지(Merge) 종류 (merge commit, squash, rebase, fast-forward)"
source: "https://jtm0609.tistory.com/185"
author:
  - "[[태크민]]"
published: 2024-09-21
created: 2026-06-28
description: "👾 Merge Commit(3-way merge)두 브랜치의 변경 사항(history)을 모두 유지하며 메인 브랜치에 다른 브랜치를 병합(merge)합니다.이 경우 각 브랜치의 변경 사항들이 과거의 커밋으로 보존되며,병합 시 메인 브랜치에 새로운 'merge commit'이 추가되며 병합이 완료됩니다.Merge Commitfeature 브랜치의 가장 최근 커밋이 메인 브랜치로 병합되는 것을 볼 수 있습니다. 아래의 그림과 같이 3개의 커밋을 고려하여 병합이 이루어지기 때문에 3-way라는 이름이 붙여졌습니다.3-way merge1번은 두 브랜치의 공통 조상이 되는 커밋,2번은 분기된 브랜치가 가리키고 있는 커밋,3번은 다른 브랜치가 가리키고 있는 커밋입니다. 장점프로젝트의 진행 상황을 명확히 추적할 수.."
tags:
  - "clippings"
---
## 👾 Merge Commit(3-way merge)

두 브랜치의 변경 사항(history)을 **모두 유지** 하며 메인 브랜치에 다른 브랜치를 병합(merge)합니다.

이 경우 각 브랜치의 변경 사항들이 과거의 커밋으로 보존되며,

병합 시 메인 브랜치에 새로운 ' **merge commit** '이 추가되며 병합이 완료됩니다.

![](https://blog.kakaocdn.net/dna/c8JTss/btsJHWzelT0/AAAAAAAAAAAAAAAAAAAAAKcUO43cVYsWAZsdPCpzkLf6fqUZTHcww60Bz3jR4BKn/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1782831599&allow_ip=&allow_referer=&signature=SJXX70093TPZUa31pCtDuv1rqS0%3D)

Merge Commit

feature 브랜치의 가장 최근 커밋이 메인 브랜치로 병합되는 것을 볼 수 있습니다.

아래의 그림과 같이 3개의 커밋을 고려하여 병합이 이루어지기 때문에 3-way라는 이름이 붙여졌습니다.

![](https://blog.kakaocdn.net/dna/pRo8A/btsJGHccutp/AAAAAAAAAAAAAAAAAAAAANzez9mInR5OHE40GJXsJz08_DjW8Oi8HpNVHmGSRhBz/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1782831599&allow_ip=&allow_referer=&signature=Ry4jYjnWWr2UuGN0%2BwitcFVlpFU%3D)

3-way merge

1번은 두 브랜치의 공통 조상이 되는 커밋,

2번은 분기된 브랜치가 가리키고 있는 커밋,

3번은 다른 브랜치가 가리키고 있는 커밋입니다.

장점

- 프로젝트의 진행 상황을 명확히 추적할 수 있음
- 브랜치 별 변경 사항이 유지되므로 커밋들의 아이디가 바뀌지 않음(아래에서 살펴볼 다른 병합 방식들보다 비교적 사용이 쉬움)

단점

- 병합이 될 때마다 커밋이 생기므로 다양한 브랜치를 병합하면 커밋 로그가 매우 복잡해 질 수 있음
- 대규모 인원이 참여하는 프로젝트에서는 복잡도가 빠르게 증가

---

## 👾 Squash and Merge

병합할 브랜치의 모든 변경 사항을 **하나의 커밋으로 합쳐** 메인 브랜치에 병합합니다.

이 경우 병합되는 브랜치의 변경 사항들은 사라지며,

모든 변경 사항이 합쳐진 한 개의 커밋이 **새로운 아이디** 를 가지고 메인 브랜치에 추가되며 병합이 완료됩니다.

![](https://blog.kakaocdn.net/dna/cyg0wS/btsJItDgIbu/AAAAAAAAAAAAAAAAAAAAAFIa1mLyAFGcPKeDmeVlJ967HZJIqGmTMGuCt9gT5xCz/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1782831599&allow_ip=&allow_referer=&signature=N8J%2FVZr7tbn3ahCBYnSkx30%2BsNI%3D)

Squash and Merge

브랜치가 분기되고 난 후에 발생한 커밋3, 4가 -> 3'로 합쳐져 하나의 새로운 커밋으로 메인 브랜치에 병합되는 것을 볼 수 있습니다.

```bash
$ git checkout <main>
$ git merge --squash <feature/branch>
$ git commit -m "squash & merge"
```

장점

- 커밋 히스토리를 간단하게 유지할 수 있음
- 각 커밋들이 합쳐지며 가장 중요한 내용이 담긴 하나의 커밋으로 압축됨

단점

- 각 커밋에 담겨 있던 세부적인 작업 이력을 잃게 됨
- 개별적인 맥락을 알 수 없으므로 문제가 발생한 부분을 찾기 어려움
- 기존에 존재하던 커밋 아이디들이 하나의 새로운 아이디로 생성되므로 기존 브랜치를 기반으로 한 작업에 문제가 생길 수 있음

> squash 방식을 사용한다고 해서 기존의 커밋 내역이 완전히 삭제되는 것은 아닙니다.  
> '깃'이 아닌 '깃허브'에는 기존의 커밋 내역들이 모두 남아있기 때문에 이전의 pull request를 찾아 해당 작업의 커밋 히스토리를 확인할 수 있습니다.

---

## 👾 Rebase and Merge

현재 위치한 브랜치를, 병합시킬 타겟 브랜치에 재위치(rebase)시킨 뒤 병합합니다.  
기준이 되는 베이스 브랜치를 변경(re-base)한다고 생각하면 됩니다.

현재 위치한 브랜치의 모든 **커밋을** **타겟 브랜치로** 옮기기 때문에 **커밋 아이디가 변경** 됩니다.

![](https://blog.kakaocdn.net/dna/bugSxh/btsJGnkMOrL/AAAAAAAAAAAAAAAAAAAAAJVM7hiKQioHhRp7hiACQmIm8VU_0bXcaxSp4i8GLJPJ/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1782831599&allow_ip=&allow_referer=&signature=JYSuT7d9ObemWFrqOoRFTiixAkg%3D)

Rebase and Merge

브랜치가 분기되고 난 후에 발생한 커밋A, B가 각각 새로운 커밋으로 메인 브랜치에 병합되는 것을 볼 수 있습니다.

```bash
$ git checkout <feature/branch>
$ git rebase <main>
$ git checkout <main>
$ git merge <feature/branch>
```

장점

- 커밋 히스토리가 선형적으로 깔끔하게 유지됨
- 깨끗한 히스토리 덕분에 변경 사항들을 쉽게 파악할 수 있음

단점

- 기존에 존재하던 커밋 아이디들이 각각 새로운 아이디로 생성되므로 기존 브랜치를 기반으로 한 작업에 문제가 생길 수 있음
- 다양한 브랜치가 존재할 경우 병합이 어려움
- 선형적이라는 특징으로 인해 특정 기능이 어디부터 어디까지의 커밋으로 구현되었는지 알 수 없음

---

## 👾 Fast-forward Merge

fast-forward란, 영상이나 소리를 빨리감기(앞으로 감기)한다는 의미입니다.

따라서 병합 시 새로운 **merge commit을 생성하지 않고**, 현재 위치한 브랜치의 최신 커밋 = **HEAD가 가리키고 있는 커밋** 이 병합할 브랜치의 **최신 커밋으로 이동** 하며 병합됩니다.

메인 브랜치에서 다른 브랜치가 분기되었지만, 다른 브랜치에서만 새로운 커밋이 생기고 메인 브랜치에서는 아무런 변화가 없는 경우 fast-forward merge가 발생합니다.

![](https://blog.kakaocdn.net/dna/4m4ya/btsJHp2XHgm/AAAAAAAAAAAAAAAAAAAAAE74eISuBJit2KjftGR_IVrU7_HO7qTWsgVZtT-c-LRL/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1782831599&allow_ip=&allow_referer=&signature=atwGJ7DMED8LM4a9ClNWGN51s9I%3D)

Fast-forward Merge

분기된 후에 메인 브랜치에는 새로운 커밋이 생기지 않았으므로 메인 브랜치와 분기된 브랜치의 커밋 히스토리는 동일한 선 상에 위치하고 있다고 볼 수 있습니다.

위에서 본 병합 방식과의 차이점은 아래와 같습니다.

- squash and merge는 다른 브랜치의 커밋들을 합쳐서 하나의 커밋으로 만든 뒤 병합하지만, fast-forward merge는 기존의 커밋들이 유지됩니다.
- rebash and merge는 메인 브랜치와 분기된 브랜치 둘 다에 새로운 커밋이 존재하는 상태에서 병합이 진행되지만, fast-forward merge는 메인 브랜치에는 변경 사항이 없는 상태에서 병합됩니다.

옵션

- \--ff: Fast-forward 병합 옵션의 기본 값. Fast-forward 병합이 가능할 때는 Fast-forward 병합을 수행하고, 불가능할 때는 다른 방법으로 병합한다.
- \--ff-only: Fast-forward 방식으로만 병합한다. Fast-forward 병합이 불가능할 경우는 병합하지 않는다.
- \--no-ff: Fast-forward 방식을 사용하지 않고 병합한다. 설령 두 브랜치가 Fast-forward 관계에 있다고 하더라도, Fast- forward 병합을 하지 않고 각각 브랜치의 변경 이력을 가지면서 병합한다.
```bash
$ git checkout main

$ git merge <branch>
or
$ git merge --ff <branch>
or
$ git merge --ff-only <branch>
or
$ git merge --no ff <branch>
```

출처

[https://dev-district.tistory.com/m/21](https://dev-district.tistory.com/m/21)

[

\[Git\] Merge의 종류(merge commit, squash, rebase, fast-forward)

👾 Merge Commit(3-way merge)두 브랜치의 변경 사항(history)을 모두 유지하며 메인 브랜치에 다른 브랜치를 병합(merge)합니다.이 경우 각 브랜치의 변경 사항들이 과거의 커밋으로 보존되며,병합 시 메

dev-district.tistory.com

](https://dev-district.tistory.com/m/21)

[https://medium.com/depayse/git-github-git-%EA%B8%B0%EB%B3%B8-%EB%AA%85%EB%A0%B9%EC%96%B44-merge-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B3%91%ED%95%A9%ED%95%98%EA%B8%B0-e6150f1d3f82](https://medium.com/depayse/git-github-git-%EA%B8%B0%EB%B3%B8-%EB%AA%85%EB%A0%B9%EC%96%B44-merge-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B3%91%ED%95%A9%ED%95%98%EA%B8%B0-e6150f1d3f82)

[

\[Git/Github\] Git 기본 명령어4 — merge(브랜치 병합하기)

merge는 브랜치와 브랜치를 병합할 수 있는 명령이다. 두 브랜치를 병합한다는 것은 다른 브랜치의 변경사항을 현재 브랜치에 적용시키는 것을 의미한다.

medium.com

](https://medium.com/depayse/git-github-git-%EA%B8%B0%EB%B3%B8-%EB%AA%85%EB%A0%B9%EC%96%B44-merge-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B3%91%ED%95%A9%ED%95%98%EA%B8%B0-e6150f1d3f82)

[https://hudi.blog/git-merge-squash-rebase/](https://hudi.blog/git-merge-squash-rebase/)

[

Git의 다양한 브랜치 병합 방법 (Merge, Squash & Merge, Rebase & Merge)

학습 배경 우아한테크코스 달록팀에서 브랜치 전략 개선과 배포와 관련된 이야기를 하면서 다양한 병합방법에 대한 이야기가 나왔다. 이야기를 해보면서, 아직 Git의 다양한 병합 방법에 대한

hudi.blog

](https://hudi.blog/git-merge-squash-rebase/)

#### 'git' 카테고리의 다른 글

| [\[Git\] 이전 커밋으로 되돌리기(reset, revert) 비교 및 사용법 정리](https://jtm0609.tistory.com/183) (1) | 2024.09.21 |
| --- | --- |
| [\[Git\] Git 영역 & 상태 정리](https://jtm0609.tistory.com/182) (1) | 2024.09.21 |
| [\[Git\] fetch 와 Pull 의 차이점](https://jtm0609.tistory.com/181) (1) | 2024.09.21 |
| [\[Git\] Git을 이용한 협업: Fork 부터 Pull Request 까지](https://jtm0609.tistory.com/180) (1) | 2024.09.21 |
| [\[Git\] Git 브랜치 전략 (feat. Git Flow)](https://jtm0609.tistory.com/179) (0) | 2024.09.21 |