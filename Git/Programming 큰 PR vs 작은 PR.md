---
title: "[Programming] 큰 PR vs 작은 PR"
source: "https://jh-bk.tistory.com/66"
author:
  - "[[Jonghyuk Baek]]"
published: 2023-03-21
created: 2026-06-28
description: "Introduction 프로젝트를 여러 명이서 진행하다 보면 필수적으로 Pull Request (흔히 줄여서 PR) 이란 것을 하게 될 것이다. PR은 개별의 개발 참여자가 작업한 코드를 메인 코드베이스에 통합하기 전에, 코드를 검토 받기 위한 일련의 과정을 의미한다. 각각의 작업자가 독립적인 환경 (피처 브랜치) 에서 작업물을 여러 커밋에 걸쳐 기록하고 나면, 이 커밋들을 메인 코드베이스 (메인 브랜치) 에 통합하기 위해 Pull Request를 생성하고, 관련된 다른 작업자들이 리뷰어로 참여해 변경 사항을 검토하고 수정 제안 혹은 최종 승인을 할 수 있다. 보통 한 가지의 독립된 기능을 구현하기 위해 피처 브랜치를 파고, 해당 기능 구현이 완료되면 피처 브랜치를 병합하기 위해 PR을 날리기 마련이다..."
tags:
  - "clippings"
---
![](https://blog.kakaocdn.net/dna/28Ser/btr40TCMgNH/AAAAAAAAAAAAAAAAAAAAAGttIVwnUMQ4m_AF1wPhPIGXcRAsw3U94KsKepF6F0uU/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1782831599&allow_ip=&allow_referer=&signature=284LDwqpwXl%2FPChgphCOcdwYF%2BQ%3D)

### Introduction

프로젝트를 여러 명이서 진행하다 보면 필수적으로 **Pull Request** (흔히 줄여서 **PR**) 이란 것을 하게 될 것이다. PR은 개별의 개발 참여자가 작업한 코드를 메인 코드베이스에 통합하기 전에, 코드를 검토 받기 위한 일련의 과정을 의미한다.

각각의 작업자가 독립적인 환경 (피처 브랜치) 에서 작업물을 여러 커밋에 걸쳐 기록하고 나면, 이 커밋들을 메인 코드베이스 (메인 브랜치) 에 통합하기 위해 Pull Request를 생성하고, 관련된 다른 작업자들이 리뷰어로 참여해 변경 사항을 검토하고 수정 제안 혹은 최종 승인을 할 수 있다.

보통 **한 가지의 독립된 기능** 을 구현하기 위해 피처 브랜치를 파고, 해당 기능 구현이 완료되면 피처 브랜치를 병합하기 위해 PR을 날리기 마련이다. 따라서 하나의 PR은 담당하고 있는 기능이 명확하며, 변경의 범위도 그렇게 작거나 크지 않은 것이 일반적이다. 하지만 작업을 하다 보면 뜻대로 되지 않는 경우가 종종 있다.

가끔씩 한 가지 기능이 엄청나게 커지거나, 여러 관련된 변경 사항을 묶어 처리하다 보면 한 PR에서 엄청나게 많은 코드 변경이 포함될 때도 있고, 혹은 한 기능을 담당하는 PR이 꼴랑 몇 줄 짜리 코드 변경을 담고 있는 경우도 있다. 물론 적당한게 가장 좋다지만... 만약 당신이 여러 가지 변경 사항을 묶어서 처리할 수도, 혹은 잘게 나누어진 개별 PR로 날릴 수 있는 상황과 마주쳤다고 해 보자. 어떤 선택을 해야 좋을까?

이번 글에서는 **큰 단위의 PR** 과 **작은 단위의 PR** 의 장단점에 대해 간단하게 알아볼 예정이다.

---

### 작은 단위의 Pull Request

먼저 작은 사이즈의 PR에 대해 알아보자.

#### 장점

**1\. 리뷰하기 쉽다**

변경 사항이 적으면 적을 수록 어떤 내용이 바뀌었고, 어떤 부분을 중점적으로 리뷰해야 하는지 파악하기 쉽다. 리뷰에 걸리는 시간과 리뷰의 품질도 단순히 코드 양이 줄어든 정도 이상으로 개선된다.

**2\. 빠르게 피드백을 받을 수 있다**

1번에서 이어져, 비교적 빠른 시간 안에 피드백을 받아 PR을 완료하고 다음 작업에 집중할 수 있다. 대형 PR의 경우 당장 리뷰를 하지 않고 미루게 되거나, 바로 한다 해도 꽤나 긴 시간을 들여 리뷰를 해야 하기 때문에 즉각적인 피드백을 받기는 쉽지 않다.

**3\. 충돌 가능성이 적다**

빠르게 리뷰를 받아 메인 코드베이스에 병합될 수 있기 때문에, 병합 과정에서 충돌 (merge conflict) 이 날 가능성이 줄어들며 다른 작업자들의 작업과 충돌할 가능성 또한 줄어든다. 또한 충돌을 해결하기도 비교적 쉽다. 이를 통한 Integration fear 감소 효과도 있다 ([참고](https://martinfowler.com/articles/branching-patterns.html#integration-fear)).

**4\. 목적이 명확하다**

변경 사항이 적을수록 변경 목적이 한 가지로 줄어들 가능성이 크고 목적이 명확하게 드러나기 때문에 (PR description 을 적을 때도 마찬가지이다), 이 PR이 무엇을 위한 건지 다른 작업자, 혹은 미래의 본인이 파악하기가 쉽다.

#### 단점

**1\. 잦은 리뷰로 인한 피로**

하루에 서너번씩 울리는 PR 알림으로 인해 팀원들의 집중력이 떨어질 수 있다. 또한 PR의 갯수가 많아지는 데에서 오는 PR 관리의 어려움도 있다.

**2\. 큰 틀에서의 작업 방향을 파악하기가 어렵다**

한 가지의 큰 목적을 가진 변경 사항들을 여러 PR로 쪼개게 되면, 각각을 열어 봤을 때 이게 어떤 전체적인 그림을 그리고 있는 건지 파악하기 어려울 수 있다. PR description에 여러 연관 PR을 링크하거나 설명을 추가할 수 있지만, 본질적인 한계는 존재한다.

### 큰 단위의 Pull Request

이번엔 반대로 큰 사이즈 PR의 장단점에 대해 알아보자.

#### 장점

**1\. 전체적인 작업 방향을 파악하기 쉽다**

작은 PR의 단점 2번과 완전히 반대되는 내용이다. 변경 내용 자체가 하나의 PR로 묶여 있기 때문에 전체적으로 어떤 목적을 위해 이 많은 변경이 이루어지고 있는지 알기가 쉽다.

**2\. 관리하기가 편하다**

PR의 갯수 자체가 적고, 각각의 PR은 어떤 큼직큼직한 목적들을 가지고 있기 때문에 나중에 특정 작업 내용을 다시 찾아보기 편하다.

#### 단점

**1\. 리뷰가 어렵다**

큰 PR은 검토하기가 어렵다. 한참을 걸려 작성한 수많은 코드를 다시 한참을 걸려 읽어봐야 하며, 점점 더 넓은 코드 영역을 리뷰 시에 커버해야 하기 때문에 리뷰의 난이도는 지수적으로 증가하게 된다. 그 만큼 리뷰의 품질이 떨어질 수 있다.

**2\. 피드백이 느리다**

1번에 이어서, 어쩔 수 없이 리뷰 시간이 늘어나 느리게 피드백을 받아야 한다. 단순히 리뷰 시간이 늘어난 것에 더해져, 큰 변경 사항을 보고 진이 빠져 리뷰어가 잠시 다른 일을 하러 가거나, 그러다가 리뷰를 까먹는다던지 갖가지 이유로 피드백을 받는 시간이 늘어질 수 있다.

**3\. 충돌 가능성이 크다**

변경 내용이 많을 수록 메인 코드베이스, 혹은 다른 작업자의 변경 내용과 충돌을 일으킬 가능성이 크다. 물론 테스트 파이프라인을 통과할 가능성 또한 낮아진다.

**4\. 세부적인 작업 내용을 파악하기 쉽지 않다.**

장점 1번과 반대로, PR에 포함된 작은 기능들에 대한 변경사항을 파악하기 어려워진다. 커밋을 기능별로 잘 나누고 커밋 메시지도 잘 적어 놓았다면 다행이지만, 그렇지 않은 경우 어떤 기능이 어느 코드라인에 해당되는지 일일이 찾아다니는 수고를 들여야 한다.

### Remarks

결과적으로는, 작은 크기의 PR과 큰 크기의 PR 모두 장단점을 지니고 있다. 개인적으로, 또는 일반적인 개발자 커뮤니티의 의견으로는 작은 PR이 개발 속도나 협업 측면에서 더 낫다고 여겨지고 있지만.. 결국 중요한 것은 당신의 팀에 어떤 요구조건이 있으며, 어떤 방식을 썼을 때 더 편하고 효율적이라 느껴지는 지일 것이다.

---

아래는 참고할 만한 페이지들이다. 모두 좋은 글들이니 시간이 나면 읽어보길 바란다.

### References

**Integration Fear**

[https://martinfowler.com/articles/branching-patterns.html#integration-fear](https://martinfowler.com/articles/branching-patterns.html#integration-fear)

**Patterns for Managing Source Code Branches**

[https://martinfowler.com/articles/branching-patterns.html](https://martinfowler.com/articles/branching-patterns.html)

**Are small pull requests better than one large all-encompasing features, and how can I convince my team of that?**

[https://softwareengineering.stackexchange.com/questions/425665/are-small-pull-requests-better-than-one-large-all-encompasing-features-and-how](https://softwareengineering.stackexchange.com/questions/425665/are-small-pull-requests-better-than-one-large-all-encompasing-features-and-how)

**Do Larger Pull Requests Receive More Extensive Reviews?**

[https://jellyfish.co/blog/larger-pull-requests/](https://jellyfish.co/blog/larger-pull-requests/)