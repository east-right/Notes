#docker #마이크로서비스 #fast-api

## 0. 한 줄 결론
무거운 의존성(torch)을 **안 쓰는 쪽이 떠안지 않게** 하는 게 핵심이고,
방법은 두 가지(모듈 분리 / lazy import)다. 이 프로젝트는 최소 변경인 lazy import를 택했다.

---

## 1. 가장 중요한 사실 — load_model은 어느 컨테이너도 호출 안 한다
흔한 오해: "load_model은 모델 서버에서 호출되니까 lazy로 둔다." → **틀림.**

- **모델 서버**(`inference/server.py`)는 search.py를 **쓰지도 않는다.** 자기 임베딩 코드를 따로 갖는다:
  ```python
  # inference/server.py — search.py와 무관
  from sentence_transformers import SentenceTransformer
  _embedder = SentenceTransformer(EMBED_MODEL, ...)
  ```
- search.py의 `load_model()`을 실제로 부르는 곳 = 로컬 테스트 스크립트(`search.py`의 `__main__`)뿐.
- 즉 **두 컨테이너 모두 load_model을 호출하지 않는다.**

| 주체 | search.py import? | SentenceTransformer 실행? | 경로 |
|---|---|---|---|
| 앱 컨테이너 | ⭕ (get_client만) | ❌ | load_model 호출 안 함 |
| 모델 서버 | ❌ (안 씀) | ⭕ | server.py가 직접 import |
| 로컬 테스트 | ⭕ | ⭕ | `__main__`이 load_model() 호출 |

---

## 2. 그럼 lazy import는 누구를 위한 건가 → "앱"
이유는 모델 서버가 아니라 **앱(LangGraph 메인 서버)** 때문이다.

- 앱은 search.py가 필요하다. 이유: `get_client`(OpenSearch 클라)를 쓰려고.
- 그런데 같은 파일에 무거운 `load_model`(torch)도 들어 있다.
- torch import가 **파일 최상단**이면 → 앱이 `get_client` 하나 쓰려고 import만 해도 torch가 딸려옴 → 9GB.

문제의 본질: **앱이 안 쓰는 무거운 코드(load_model)와 앱이 쓰는 코드(get_client)가 한 파일에 섞여 있다.**

lazy import는 이걸 푼다:
> torch import를 load_model 함수 안에 숨겨, 앱이 search.py를 import해도 get_client만 건드리고 torch는 안 건드리게 한다.

```
search.py (두 기능이 섞임)
 ├─ get_client()   ← 가벼움. 앱이 씀
 └─ load_model()   ← 무거움(torch). 아무 컨테이너도 안 씀

앱이 search.py import:
   최상단 import였다면 → import만 해도 torch 끌려옴 → 9GB ❌
   함수 안 import라면   → get_client만 쓰니 torch 안 옴 → 389MB ✅
```

> 핵심: lazy import의 수혜자는 **앱**. load_model을 누가 부르냐는 부차적.

---

## 3. 코드 짤 때의 두 갈래 (일반화)
무거운 의존성을 안 쓰는 소비자가 떠안지 않게 하는 법:

| 방법 | 어떻게 | 장점 | 단점 |
|---|---|---|---|
| **A. 모듈 분리** | get_client를 별도 파일로 빼서 앱은 그것만 import | 의존성 경계가 파일 구조에 명시됨, 깔끔 | 구조를 더 건드림 |
| **B. lazy import** | torch import를 함수 안으로 숨김 | 한 줄만 이동, 최소 변경 | 한 파일에 무거운/가벼운 게 섞인 채 남음(응집도↓) |

원칙:
> 한 모듈에 무게가 다른 기능이 섞여 있고 가벼운 쪽만 쓰는 소비자가 있다면 → 경계를 의심하라.
> 1순위: 관심사별 모듈 분리(A). 2순위(분리가 과하면): lazy import(B).

---

## 4. 이게 마이크로서비스 코드로 나쁜가? — 절충이다

**👍 잘한 점**
- 진짜 중요한 경계(추론=torch를 별도 서버로 분리)는 제대로 했다. 앱은 임베딩을 HTTP(`/embed`)로 위임하지 직접 안 한다. 이게 마이크로서비스의 본질이고 잘 지켜졌다.
- 그 덕에 앱 이미지에서 torch 제거가 가능해졌다. lazy import는 그 위의 마감 작업.

**👎 냄새(code smell)**
- search.py 하나에 두 서비스의 관심사(get_client + load_model)가 섞여 응집도가 낮다.
- lazy import는 섞인 걸 둔 채 증상만 가린 것. 더 깨끗한 건 A(분리).
- lazy import의 함정: import 에러가 실행 시점까지 미뤄짐 / import 비용이 첫 호출 때 발생 / 정적 분석 도구가 의존성을 놓침.

---

## 5. 면접용 정리
> "추론을 별도 서버로 분리한 핵심 경계는 잘 잡혔다. 다만 OpenSearch 클라이언트와 임베딩 로더가 한 파일에 남아 응집도가 낮고, lazy import는 그 상태에서 앱이 torch를 안 떠안게 하는 최소 변경 해법이다. 이상적으로는 get_client를 별도 모듈로 분리해 의존성 경계를 파일 구조에 명시하는 게 더 깨끗하다. 지금은 '한 줄 이동 vs 리팩터링'의 트레이드오프에서 실용성을 택한 것."

> 포인트: "알고도 절충했다"를 보여주는 게 모르고 한 것보다 점수가 높다. 면접관은 완벽한 코드보다 트레이드오프를 인지하는 사람을 본다.

---

## 6. 왜 마이크로서비스에서 이 사고가 중요한가
서비스를 쪼개면 공유 코드(search.py)가 양쪽에 끌려간다. 한쪽에만 필요한 무거운 의존성이 공유 코드에 박혀 있으면 안 쓰는 서비스까지 뚱뚱해진다.
→ **공유 모듈은 의존성을 최소·균일하게 유지**하는 게 원칙.