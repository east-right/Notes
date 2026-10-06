#LLM #text2sql #amazon
URL: [aws.amazon.com/ko/…build/](https://aws.amazon.com/ko/events/aws-innovate/apj/migrate-modernize-build/)
유튜브 링크: [Youtube](https://www.youtube.com/watch?v=BpggzDtxBXg)
## 0.요약
- 스키마 설명문서, 테이블 스키마 정보와 같은 MetaData와 예시 코드를 Context로 제시를 하는 것이 핵심
- 생성된 SQL을 바로 반환하지 않고 결과 값을 같은 LLM이나 서브 LLM에 input으로 넣어서 검증 단계를 거침

## 1. Text-to-SQL 개요
- Client(업무 요청) ↔ Data Engineer(SQL) ↔ Database
- Text-to-SQL
  - “규칙 기반 시스템” + “기본적인 자연어 처리 기술”
  - 사용자의 자연어 질문을 쿼리로 변환
- Text-to-SQL with LLM
  - LLM을 활용해서 사용자의 자연어 질문을 더 정확하고 복잡한 SQL 쿼리로 변환하며, 높은  유연성과 학습 능력을 제공하는 기술
## 2. Text-to-SQL 준비
Text-to-SQL 준비 참고:https://github.com/kevmyung/db-schema-loader
### 2.1 사전 지식
1. LLM: 거대한 양의 데이터를 사전 학습하여, 자연어 이해 및 코드 작성 능력 보유
2. In-Context Learning: SQL쿼리 작성을 위해 데이터베이스 정보를 Prompt 내 Context로 제공
3. RAG: 수많은 정보 중 사용자 질문과 연관된 사전정보를 추출해서 LLM에 제공
4. Schema Linking: 사용자 질문에 맞는 SQL쿼리 작성에 필요한 Schema 요소 선택
### 2.2 주요 컴포넌트
1. DB: 자연어로 쿼리할 데이터 저장소(ex. RDB, Redshift, Athena)
2. Metadata: 데이터베이스로부터 추출된 Schema, DB 정보, 샘플 쿼리 등
3. Vector Store (for RAG)
   - DB 스키마 요소에 대한 설명 문서를 저장 및 검색
   - 과거 사용된 자연어 질문 및 샘플 쿼리 정보를 저장 및 검색
   - Amazon Opensearch Services
	1. Prompt: 쿼리 생성에 참고할 Context 및 사용자 요구사항 해결의 지침 제공
	2. LLM
	   - 사용자의 질문을 이해하여 이에 상응하는 SQL 쿼리 생성
	   - Amazon Bedrock을 활용한 다양한 모델 활용
### 2.3 효과적인 Text-to-SQL을 위한 준비사항
1. 목적에 맞는 스키마 선택과 LLM이 자연어 질문을 잘 이해할 수 있도록 스키마 설명 문서 중요
2. 조회할 데이터베이스의 스키마 설명 문서 준비
   - SQL 쿼리나 DB 관리 도구로 스키마 정보 추출 → 스키마 설명 문서 정의된 파일(csv, xlsx 등)
   - 스키마 정보 부족 시 LLM으로 증강하여 구체적인 설명 문서 생성
### 2.4 Schema Linking 구현
1. LLM에 테이블 스키마 정보 제공: LLM은 테이블 스키마 정보를 참조해서 자연어 질문을 정확히 해석하고 올바른 SQL 쿼리를 생성
2. LLM에 샘플 쿼리 제공
   - 자연어 질문과 그에 해당하는 쿼리 (input, query)를 가지고 있는 경우 샘플로 활용
   - 쿼리만 있고 질문(input)은 없는 경우: LLM을 활용하여 SQL에 해당하는 자연어 질문 생성
     - 자연어 질문이 포함된 샘플 SQL 쿼리 확보 가능
## 3. Text-to-SQL 아키텍처 패턴
### 3.1 Text-to-SQL 작업 패턴
1. In-Context Learning이고 프롬프트 엔지니어링으로 SQL을 생성하는 패턴
2. 사용자가 했던 자연어 text 질문과 테이블 스키마 정보, 샘플 쿼리를 포함해서 주석이 달린  예제를 프롬프트로 LLM에게 제공
3. LLM은 프롬프트를 사용해서 생성한 SQL을 반환
4. 생성된 SQL은 검증을 마치고 연결된 데이터베이스에 실행
5. 사용자는 SQL 수행 결과 return
### 3.2 Text-to-SQL의 Workflow(4단계로 구성)
#### 3.2.1 샘플 쿼리 참조
1. 사용자의 질문과 유사한 쿼리를 벡터 스토어에서 참조 
2. 샘플 쿼리와 질문을 프롬프트에 포함

```python
# Prompt
'''
"input": "3월 거래 내역 조회",
"query": "SELECT...",
"input": "5월 매출 조회",
"query": "SELECT..."
'''
<사용자 질문>
"5월 거래 내역에 대해 알려줘"
```

3. LLM이 샘플 쿼리에서 유사도 높은 쿼리를 찾는다면 추가 작업 없이 쿼리 생성 가능

#### 3.2.2 Schema Linking
샘플 쿼리만으로 쿼리 생성 불가할 경우 스키마 설명 문서 참조하는 후속 작업 필요  ⇒ 쿼리를 생성하기 위해 사용자 요청에 맞는 스키마 링킹 작업이 선행되어야 함
1. 벡터스토어의 스키마 정보에서 사용자 질문과 유사도 높은 Top k의 테이블과 그 설명을 조회
2. DB로부터 샘플 레코드 몇 개를 담아 프롬프트에 담아줌
3. 스키마 링킹 프롬프트를 LLM에게 제공

```json
# Prompt
<테이블+컬럼 설명+샘플데이터>
{
	"Table 1": [
		{
		"Column 1": "Column1 설명"
		},
	...
	],
	"Table 3": [
		{
		"Column1": "Column1 설명"
		},
	...
	]
}
<사용자 질문>
"5월 거래 내역에 대해 알려줘"
```
4. LLM은 쿼리 생성에 필요한 테이블 및 컬럼 이름 등을 선별
#### 3.2.3 쿼리 생성 및 검증
- 스키마를 선정한 이후, 쿼리를 생성하고 검증하는 단계
1. 애플리케이션은 스키마 링킹으로 선택된 스키마 정보, 샘플 쿼리, 유저 질문, DB 종류를 프롬프트에 담아 LLM에게 쿼리 생성 요청
2. LLM은 사용자 질문에 맞는 SQL 쿼리를 리턴
3. 리턴 받은 쿼리는 검증 과정을 거침 → 같은 LLM 혹은 서브 LLM 사용
4. 검증 이후 최종 쿼리를 응답 받음
#### 3.2.4 수행 및 답변 생성
1. 애플리케이션은 데이터베이스에 최종 쿼리를 실행하고 그 결과를 얻어냄
2. 애플리케이션이 결과와 전체 또는 일부(Top K) 샘플 데이터를 LLM에게 전달
3. LLM은 쿼리 수행 결과를 바탕으로 유저에게 제공할 답변을 생성
4. 애플리케이션은 해당 답변을 유저에게 제공
### 3.3 AWS 리소스를 활용한 Text-to-SQL 아키텍처
![](assets/Pasted%20image%2020250402162428.png)
- Text-to-SQL 애플리케이션이 구동될 컴퓨트 영역
  - Amazon EC2, EKS, ECS 같은 컨테이너 혹은 서버리스 서비스인 Lambda

- DB 메타데이터 관리를 위한 벡터 스토어
  - Amazon OpenSearch Service 사용
  - 자연어 벡터 변환을 위한 Bedrock 임베딩 모델이 함께 활용됨

- 생성된 쿼리로 접근할 데이터 영역
  - Amazon Redshift, Athena 등 다양한 서비스를 대상으로 가능

1. 사용자는 애플리케이션(컴퓨트 영역)에 질문을 전달
2. 샘플 쿼리 검색, 스키마 정보 조회
3. 애플리케이션은 Text-to-SQL Workflow의 수행 작업에서 Amazon Bedrock의 생성형 AI 언어 모델과 여러 차례 프롬프트를 주고 받음
4. Amazon Bedrock의 생성형 AI 언어 모델과 여러 차례 응답 또한 주고 받음
5. SQL 실행
6. 결과 답변 및 Workflow 종료

## 4. 데모 및 추가 최적화 고려 사항
### 4.1 데모 애플리케이션
데모 애플리케이션 참고: https://github.com/kevmyung/text-to-sql-bedrock
Amazon Bedrock, 벡터 스토어, 데이터베이스에 연결해서 Text-to-SQL 작업 수행 예정

#### 4.1.1 데모 준비
1. 모델 선택: Claude 3 Sonnet
2. 데이터베이스 URI 지정
   - 사용할 데이터베이스 프로토콜과 인증 정보, 호스트 이름을 제공하여 Text-to-SQL  애플리케이션이 SQL 쿼리 생성한 후 원하는 데이터베이스에 접근할 수 있도록 함
3. 스키마 설명 문서 준비 상태 확인
   - 	자연어 질문과 SQL로 구성된 샘플 쿼리(chinook_example_queries_temp.json)
   - 테이블, 컬럼 등 스키마에 대한 상세한 설명 문서(chinook_detailed_schema_temp.json)
   - 자연어 검색에 용이하도록 Amazon OpenSearch Service에 인덱싱 할 예정
4. 처리하기 버튼
   - 처리하기 버튼을 눌러 JSON 문서를 OpenSearch Service에 인덱싱
   - 자연어를 벡터 임베딩으로 변환하는 전처리 작업이 포함됨
5. 결과물을 OpenSearch 개발자 도구에서 확인
   - 샘플 쿼리에 대한 자연어 질문과 이에 매핑되는 벡터 임베딩 필드 확인 가능
   - 스키마 설명 문서에는 테이블에 대한 자세한 요약 정보가 텍스트와 벡터 임베딩 필드로 함께 저장됨

#### 4.1.2 Text-to-SQL 애플리케이션 실행
1. 사용자가 질문 제공: 2023년에 가장 많이 구매한 고객은?
2. Text-to-SQL 애플리케이션은 벡터 스토어인 OpenSearch를 검색하여 사용자의 질문과 유사한 샘플 쿼리를 찾아냄
3. (화면 표시 없음) 사용자 요청을 해결하기 위해 필요한 스키마 정보도 찾아냄
4. 찾아낸 스키마 정보는 프롬프트의 context로 제공
5. LLM은 context에 제공된 스키마 정보와 샘플 쿼리를 참조해서 사용자의 자연어 요청에 해당하는 SQL 쿼리를 작성하고 리턴하는 것을 확인할 수 있음
   - 쿼리 실행 전에는 생성된 쿼리가 사용자의 질문에 부합하는지, 사용할 DB와 문법적으로 호환 가능한지 검사하는 validation 과정을 포함하는 것이 좋음
   - 이 데모에서는 validation 수행 후 문제가 없을 경우에만 쿼리 수행하도록 구성되어 있음
   - 쿼리 수행 실패 시 재시도 로직 추가 필요
6. 쿼리 작성된 이후에는 이 쿼리를 데이터베이스에 실행해서 데이터를 획득
7. 획득한 데이터를 사용해 유저 요청에 대한 답변 제공 가능

#### 4.1.3 Text-to-SQL 애플리케이션 확장 - 획득한 데이터를 시각화
1. Analyze 버튼 클릭하여 시각화 페이지로 전환
2. LLM에 추가적인 프롬프트와 함수 호출을 실행하여 다양한 확장 기능 구현 가능

### 4.2  Text-to-SQL 최적화 고려사항
1. DB 메타데이터 품질 관리
   - RAG 패턴에서 사용자 질문에 부합하는 스키마 정보와 샘플 쿼리를 찾기 위해 DB 메타데이터,  즉 스키마 및 샘플 쿼리에 대한 지속적인 품질 관리 필요
   - 예) 운영 중 발생하는 스키마의 변경 사항이나 추가되는 쿼리 패턴을 샘플 쿼리에 포함해서 벡터 스토어에 저장하는 것이 좋음

2. 접근 패턴에 맞는 Workflow 설계 - 비용과 성능 면에서 유리하게 설계
   - 현실에서 SQL로 접근하는 다양한 워크로드 패턴이 있는 만큼, 쿼리 및 스키마 복잡도에 따라 Text-to-SQL Workflow의 복잡도 역시 다르게 설계되어야 함
	   - 예) join이 자주 발생하는 복잡한 쿼리 접근 패턴의 경우, Foriegn Key나 인덱스 정보를 포함하는 프롬프트를 구성
	   - 예) 데이터 마트 형태의 간단한 접근 패턴에는 Text-to-SQL 역시 단순하게 설계

3. 쿼리 검증 및 실행 단계에서 LLM이 잘못된 산출물을 만들어내고 DB에서 쿼리 실패를 리턴할 수 있음
   - 이 경우에 대비하여 Retry할 Fallback 전략을 로직으로 포함시키고 Max Retry 횟수 역시 제한하는 것이 애플리케이션의 신뢰성 향상에 도움이 됨
	   - 예) Schema Linking 중 정확한 컬럼이나 테이블을 찾지 못했다면, 이를 찾을 수 있는 다른 전략을 Retry 로직에 포함시킬 수 있음

4. Text-to-SQL의 성능을 검증하기 위해 난이도별 검증 데이터셋을 준비하여 충분한 검증 필요
   - 다양한 쿼리 패턴 중 어떤 워크로드에서 잘 동작하지 않는지 체계적으로 파악하고 이에 맞는 프롬프트 또는 rh? 패턴(무슨말이지)을 사용하도록 조절할 수 있음



<iframe sandbox="allow-scripts allow-same-origin" aria-hidden="true" tabindex="-1" referrerpolicy="no-referrer" src="https://aif.notion.so/aif-production.html" style="outline: 0px; box-sizing: border-box; color: rgb(0, 0, 0); font-family: sans-serif; font-size: medium; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial; opacity: 0; width: 1px; height: 1px; top: 0px; left: 0px; border: none; display: block; z-index: -1;"></iframe>