## 0. 결론 한 줄
앱 이미지가 9GB였던 건 torch(수 GB)가 딸려왔기 때문이고,
무거운 import를 함수 안으로 내리는 **lazy import** + torch를 뺀 **slim requirements** 조합으로 389MB로 줄였다.

---

## 1. load_model이 뭐 하는 함수인가
```python
# keyword_selection/search.py
def load_model():
    from sentence_transformers import SentenceTransformer  # lazy: torch는 여기서만
    return SentenceTransformer(MODEL_NAME, token=HF_TOKEN)
```
- 하는 일: **BGE-M3 임베딩 모델을 메모리에 올려 반환.**
- 이 모델로 `model.encode("문장")` → **문장을 벡터(숫자 1024개)로** 변환.
- SentenceTransformer가 내부적으로 torch를 쓴다 → **이 함수를 호출하면 torch가 따라온다.**

---

## 2. 핵심 개념: "import한다 ≠ 함수가 실행된
- `def load_model():` 은 **"이런 함수가 있다"고 등록만** 한다. 안의 코드는 안 돌아간다.
- 안의 코드(`from sentence_transformers import ...`)는 누군가 **`load_model()` 이라고 괄호 붙여 호출할 때** 비로소
실행된다.

```python
def load_model():
    from sentence_transformers import SentenceTransformer  # 이 줄은
    ...                                     )을 호출해야 실행됨

# 파일을 import만 하면:
#   - "load_model 함수가 있구나" 등록만 됨
#   - 함수 안의 import 줄은 절대 안 돌아감   ← 포인트
```

비유: 레시피 책에 "토치(torch) 케이크 만드는 법" 페이지가 있어도, 그 페이지대로 안 만들면 토치를 꺼낼 일이 없다. 책을 책장에 꽂는 것(import) ≠ 레시피 실행(호출).

---

## 3. 앱은 load_model을 호출하지 않는다 (코드 근거)
앱은 임베딩을 **직접 안 하고 모델 서버(:8001)
```python
# service/nodes/rule_select.py
def _embed(text: str) -> list[float]:
    resp = _get_http().post("/embed", json={"texts": [text]})  # ← HTTP로 모델 서버 호출
    resp.raise_for_status()
    return resp.json()["vectors"][0]
```
그리고 search.py에서 가져오는 것도 둘뿐:
```python
from keyword_selection.search import INDEX_NAME, get_client   # load_model은 안 가져옴
```
→ 앱 프로세스에서 `load_model()`을 호출하는

---

## 4. torch가 앱에서 안 깔리는 흐름
```
1. 앱이 rule_select.py 실행 → search.py를 import
2. search.py가 위→아래로 실행되며 함수들을 "등록"만 함
     def get_client():  등록 (몸통 실행 X)
     def load_model():  등록 (몸통 실행 X)  ← 안의 torch import 안 돌아감
     def search():      등록 (몸통 실행 X)
3. 앱은 get_client()만 호출, load_model()은
4. load_model() 몸통이 한 번도 안 돌았으니, 그 안의
   'from sentence_transformers import ...' 도 실행 안 됨
5. → torch가 import될 일 없음 → requirements
```

---

## 5. 대조: 옛날 코드는 왜 9GB였나
```python
from sentence_transformers import SentenceTransformer  # 파일 최상단 (옛날)
```
- 모듈 **최상단** 코드는 **import하는 순간
- 그래서 앱이 search.py를 import만 해도(load_model을 안 불러도!) 이 줄이 돌아 torch가 끌려옴 → 9GB.
- 이 한 줄을 **함수 안으로 내린 것(lazy impo드"가 성립.

| | import 위치 | 앱이 search.py import 시 torch 로드? |
|---|---|---|
| 옛날 (9GB) | 파일 최상단 | ⭕ 무조건 (최상단은 import 시 실행) |
| 지금 (389MB) | `load_model` 함수 안 | ❌ load_model() 호출해야만 — 앱은 호출 안 함 |

---

## 6. 다이어트가 완성되는 짝꿍: slim requirements
lazy import만으로는 이미지가 안 줄어든다. 둘이 짝으로 작동한다.
```
# service/requirements.txt — torch도 sentence-transformers도 없음
fastapi, uvicorn[standard], pydantic, python-dotenv,
httpx, langgraph, openai, opensearch-py, lan
```
- lazy import 안 한 채 requirements에서 torch만 빼면 → 앱이 search.py import 시 최상단 import가 `ModuleNotFoundError:
torch`로 **터짐.**
- lazy import로 옮겼기에 → 최상단에 무거운 import가 없어 안 터지고 → requirements에서 torch를 안전하게 제거 가능 → **이미지에 torch 미설치** → 9GB→389MB.

```
lazy import (코드)        +   slim requirements (의존성 목록)   =  389MB
  import 시 안 터지게          torch 설치 자
```

---

## 7. 왜 이미지 크기가 중요한가
- **배포 속도**: 레지스트리 push/pull이 9GB면 느림, 389MB면 빠름.
- **콜드스타트**: Modal scale-to-zero는 요청 동 빠름.
- **비용**: 레지스트리 저장·전송(트래픽) 비용.
- **보안 표면**: 안 쓰는 torch+CUDA가 없으면 취약점 노출도 감소.

---

## 8. 남은 과제
앱 이미지는 389MB로 날씬해졌지만, **모델 서버 이미지는 여전히 9.16GB**(거긴 torch가 진짜 필요).
모델 서버 다이어트는 **멀티스테이지 빌드 + CPU-only torch index**가 후속 과제.

---

## 9. 면접용 한 문장
> "search.py는 OpenSearch 클라이언트(get_client)와 임베딩 로더(load_model)를 둘 다 갖는데, 앱은 전자만 쓰고 임베딩은 모델 서버에 위임한다. import가 최상단에 있으면 앱이 파일을 import하는 것만으로 torch가 딸려오므로, import를 load_model 함수 안으로 옮겨(lazy import) 앱이 그 함수를 호출하지 않는 한 torch가 로드되지 않게 했다. 그래서 앱 requirements에서 torch를 빼 9GB→389MB로 줄였다."