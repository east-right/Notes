#추천시스템 #벡터디비 #그래프 #알고리즘 #자료구조 

## 0. 들어가기

- 그래프 기반 유사도 모델의 가장 대표격인 HNSW 모델이다.
- 추천 시스템 뿐만 아니라 Vector DB에 사용되는 similarity search 기능에 사용되며, 네이버가 사용할 정도로 매우 빠른속도에 괜찮은 정확성을 보장해준다.
- 논문 자체는 2016년도 논문이지만 이게 근간이 되어 similarity search에 많이 사용되는거 같다.
- 논문 주소는 https://arxiv.org/abs/1603.09320 에서 확인 가능하다.
- 오래된 개념이라 쉽게 설명한 페이지들이 많아서 거기껄 참고 하겠다.
- HNSW 는 NSW라는 개념과 Skip-list  개념을 혼합한 개념이다.

## NSW(Navigable Small World)

### Small World

- Small World(작은 세계)는 심리학자 스탠리 밀그램의 실험에 기반한다.
- 18998년 던컨 와츠와 스티븐 스트로가츠가 발표한 논문'[Collective dynamics of small-world networks](https://www.nature.com/articles/30918)'을 통해 알려진 네트워크 이론 중 하나이다.
- Small World는 **인간 관계에서 몇 단계만 거치면 서로 연결되어 있다.**는 것을 설명한 이론이다.
- Small World는 해당 이론을 바탕을한 '케빈 베이컨 게임'으로도 잘 알려져있다.
- 케빈 베이컨은 상당한 다작 배우로 보통  몇 단계(보통 6단계)정도 건너면 대부부느이 배우와 연결된다고 하여 만들어진 게임이다.

<img src="https://blog.kakaocdn.net/dn/biwcdZ/btsFgOlJvu7/uKeEJHa3t51UoPKPfkkpn1/img.png" alt="img" style="zoom: 33%;" />

> 출처: https://jerry-ai.com/30

- Small world의 이론적 특징은, 평균 경로 거리가 작고  **군집 계수(Cluster coefficient)가 높다는 것이다.**

> 군집계수(Cluster coefficient, 혹은 클러스터링 계수)란?
>
> ![img](https://blog.kakaocdn.net/dn/kLm2b/btrrUPdZB9S/fb1brdFVQK0kbOWOF6Sczk/img.png)
>
> - 위는 군집계수의 수식이다.
> - 군집 계수란 **해당 노드에 이웃하는 노드들이 얼마나 잘 연결되어 있는지를 측정하는 measure이다.**
> - `d_u`는 노드 u의 degree(노드에 연결된 엣지 수의 합, 즉 노드가 몇개의 이웃을 가지는지)를 의미하고, N(u)는 노드 u의 이웃 노드를 뜻한다.
> - 그리므로 분보는 노드 u의 이웃 노드들끼리의 노드쌍의 경우의 수를 의미하고, 분자는 그 중 연결된 엣지의 수를 의마한다.
> - 즉 군집 계수가 1에 가까울수록 모든 이웃 노드들이 서로 연결되어 있는 것이고, 0에 가까울 수록 모든 이웃 노드들이 서로 연결되어 있지 않는 것 이다.
> - 자세한건 참조 페이지 참조 
>
> > 참초: https://process-mining.tistory.com/152

- 평균 경로 거리가 짧다는 것은, 네트워크에서 임의의 두 사람을 골라도 적은 단계로 연결될 수 있다는 것을 의미한다.
- 군집 계수가 높다는 것은 어떤 사람의 친구들이 그 친구들 서로도 아는 사이일 가능성이 높다는 것을 의미한다.

### NSW(Navigable Small World)

- KNN 방식은 고차원 데이터를 다룰 때나 업데이트가 잦은 실시간성 데이터에서 낮은 효율성을 가진다.
- 새로운 데이터가 추가 될 때 마다 모든 데이터에 대해 유사도를 다시 계산해야하기 때문이다.
- KNN 기반 검색은 고차워 데이터에 대한 차원의 저주를 보이며, 동적 데이터에는 적절하지 않은 방식이다.
- 이를 개선 하기 위해 ANN 방식의 알고리즘들이 제안된다.

![img](https://blog.kakaocdn.net/dn/kzHny/btsFoaOMb8p/5HOgn42QyhkCG6kwSjn7Mk/img.png)

> 출처: https://jerry-ai.com/30

- NSW(Navigate Small world)알고리즘은 상기한 문제점을 보완하기 위해, small world 개념을 이용한다.
- 검색과정은 임의로 선택된 진입노드(entry point)에서 시작하여 지정한 수준까지 탐색을 수행한다.
- small world 이론에 입각하여, 적은 단계의 탐색만 거쳐도 충분히 쿼리라인데 근사한 노드를 반환이 가능하다.
- 이렇게 검색 양을 제한하여 많은 데이터에 대해서 KNN 기반 검색 시스템에 기존 방식에 비해 빠른 인덱싱 및 검색이 가능하다.

#### NSW를 만드는 법

![img](https://pangyoalto.com/content/images/2023/09/1_vzPqZFdw3uxZMJJa1X7IqQ.webp)

> 출처: https://pangyoalto.com/faiss-1-hnsw/

- NSW 그래프는 데이터셋을 섞고 현재 그래프에 하나씩 넣으면서 만든다.
- **새로운 정점(vertex, node)이 들어올때마다 M개의 가까운 정점과 연결한다.(위 그림에선 M이 2이다.)**
- **NSW 그래프에서 긴 edge(연결 선)가 graph navigation에서 중요한 역할을 한다.**
- 위 그림의 경우 AB가 초반에 추가됨으로 긴 edge가 만들어 졌다. 이 edge가 그래프의 한쪽에서 반대쪽으로 넘어가는 것을 쉽게해주는데 이게 핵심이다.
- 만약 긴 edge가 없다면 만약 쿼리 노드와 엔트리 노드의 거리가 멀때 시간도 오래걸리고 부정확한 local minimum에 빠질 확률이 크다. **local minimum을 예방하기 위해서 NSW는 이와 같은 그래프 생성방식을 사용하는데 local minimum의 이유는 아래 탐색 부분에서 설명이 나와있다.**
- 하지만 예방이지 완전히 지울수는 없다.
- **이러한 긴 edge는 그래프 생성 초반에 만들어진다는 큰 특징이 있다.**

#### NSW 그래프의 탐색

![img](https://pangyoalto.com/content/images/2023/09/5ca4fca27b2a9bf89b06748b39b7b6238fd4548c-1920x1080.png)

> 출처: https://pangyoalto.com/faiss-1-hnsw/

- 이렇게 생성된 그래프에서 탐색의 제일 시작은 미리 정의된 무작위 포인트인 entry point에서 탐색을 시작한다.
- 먼저 entry point와 연결된 노드를 쿼리 벡터와 비교하여 쿼리 벡터와 최대한 가까워 지게하는 edge를 선택하여 이동한다.
- 이동한 후 쿼리 벡터와 기존 노드의 거리를 다른 노드와 비교하여 가깝지 않으면 edge를 통해 다음 노드로 이동한다.
- 각 스텝에 이러한 이동을 반복하면서 쿼리벡터와 가장 가까운 노드로 이동일하고 이러한 방식을 **greedy routing**이라고 한다.
- 하지만 **Greedy routing 결과로 나온 정점은 쿼리 벡터와 가장 가까운 정점이라고 보장할 수 없다**
- 어느 분야등 greedy한 방식의 고질적인 문제로 현 상황에선 최고의 선택일지 몰라도 전체적으로 그 선택이 최종결과에 최적의 선택이 아닐 확률이 존재하기 때문이다. 
- 결국 한번 꼬이기 시작하면 local minimum 상태에 빠기게 되고, 어느정도 가까울수는 있어도 제일 가깝지 않은 노드가 선택될 확률이 다분하다.
- 이러한 문재를 해결하기 위해 NSW 위에서 설명한 그래프 생성방식을 사용한다.

## Skip list

- Skip list는 자료구조 개념중 하나이다.
- Skip list 특별한 알고리즘 자료구조가 아니라 Data Structure(데이터 구조) 자체가 알고리즘을 구현하는 방법이 되는 방법이다.
- 즉 데이터를 Skip list 형식으로 구현을 하면 그 데이터 구조가 특이해서 결국 데이터 서치가 특정한 알고리즘형식이 된다.

### Linked list

> 참조: https://opentutorials.org/module/1335/8821

- 메모리 구조에는 array list와 Linked List 이렇게 두개가 존재한다.

![img](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/1335/2903.png)

> 출처:  https://opentutorials.org/module/1335/8821

- Araay list는 우리가 흔히하는 형식으로 연결되는 데이터들이 모두 한 곳에 모여 있다. 그래서 새로운 데이터가 들어와 공간이 필요하다면 그 공간을 찾아 연결된 데이터 전부에 새로운 공간을 할당해야한다.

![img](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/1335/2928.png)

> 출처: https://opentutorials.org/module/1335/8821

- Linked List는 연결되는 데이터들이 모두 한 곳에 저장할 필요 없다.
- 빈 공간이 있으면 위와 같이 그냥 대충 들어가면 된다.
- 하지만 array랑 다르게 연결되어야 할 데이터들이 전부 떨어져 있기에 linked list 형식의 데이터 들은 본인의 value와 본인 다음 데이터의 위치(혹은 이동해야하는 방향) 총 두가지 데이터를 들고 있어야 한다.
- 즉 linked list 방식으로 데이터를 구성하고 데이터를 찾기 위해선 사람한테 물어서 길을 찾아 목적지를 찾는거 처럼 데이터 노드들의 순서를 쫓아서 찾아가야한다.

![img](https://s3.ap-northeast-2.amazonaws.com/opentutorials-user-file/module/1335/2939.png)

> 출처: https://opentutorials.org/module/1335/8821

- Linked list 구조는 위와 같다. Linked list는 array랑 다른점이 두 가지 이다.
  - **Head가 존재한다**. Head는 Linked list의 첫 번째 노드를 의미한다. 데이터를 탐색하기 위해선 Head가 가르키는 노드를 찾아 거기서 부터 원하는 데이터를 찾는다.
  - **Linked list 형식의 노드(array에선 속성)는 두 가지의 정보를 가지고 있어야하는데 하나는 본인 노드의 값과, 다음 노드가 어떠한 노드인지(다음 노드 위치)이다.**
- Linked list 방식은 Array list 방식보다 데이터 추가와 삭제 부분이 빠르지만 탐색은 느리다. 그래서 적절한 방식을 찾아야한다.
- 조금 더 자세한 내용은 https://opentutorials.org/module/1335/8821 를 참조하면 된다.

### Skip list

- Skip list는 Linked list의 단점인 검색의 속도를 높히기 위한 방식이다.
- Linked list는 데이터를 찾기 위해선 Head에서 부터 순서대로 탐색하면서 이동하기에 검색의 속도가 느리다.

![img](https://blog.kakaocdn.net/dn/OJWFQ/btqy6UlHdGe/KkgjhaUXipXui3CGuawat1/img.png)

> 출처: https://ohgym.tistory.com/10

- 위 그림은 가장 기본적인 Linked list 형식 데이터 연결 그림이다.
- 이 방식은 순서대로 이동을 해야하기에 데이터 검색의 속도가 느리다.

![img](https://blog.kakaocdn.net/dn/bIZkJj/btqy7KWXVoR/xjKtqaijIkaDxNJvQ0QR3k/img.png)

> 출처: https://ohgym.tistory.com/10

- 그래서 위 그림같이 **새로운 메모리에각 노드의 정보에 바로 다음 원소를 가르키는 정보 제외 다른 노드를 가르키는 정보를 하나 더 만든다.**
- 그림과 같이 메모리에 두 정보를 가지고 있는데, 하나는 모든 정보를 들고 있는 메모리와 몇몇 메모리만 들고 있는 메모리 총 두개를 가지게 된다.

![img](https://blog.kakaocdn.net/dn/bdYIbY/btqy4q0nTl0/kyzVsGNr6da2n45X2VS271/img.png)

> 출처: https://ohgym.tistory.com/10

- 그럼 똑같이 위와 같이 또 새로운 메모리에 또 기존 방식보다 정보량은 적지만 다른 형식의 노드의 방향 정보를 만든다.
- 이러한 방식으로 데이터 구조를 구성하면 결국 복잡해지기에 Linked list의 장점이 퇴색될 확률이 높다.
- 그래서 여기서  무작위성(Randomized)을 추가한다. 이 부분에 대해서는 깊게 다루지 않겠다. 무작위성을 추가하는 이유는 자료구조 측면에서 추가 삭제의 성능을 떨어트리지 않기 위한거니깐
- 그래서 결국 아래와 같은 형식으로 구성되고 작동한다.

![img](https://blog.kakaocdn.net/dn/bd5tON/btqy651BwN1/AWr691g3gaYsdtKkkXg7bK/img.png)

> 출처: 출처: https://ohgym.tistory.com/10

![img](https://blog.kakaocdn.net/dn/mcvK7/btsFho9i8u3/fhiWLMgLk4gdOhs1m3XP8k/img.gif)

> 출처:https://jerry-ai.com/30

- 이러한 Skip list 방식은 기존 Linked 방식보다 검색적인 측면에서 더욱 효율적이다.
- 내가 위에선 각 메모리에 각각 다른 노드 데이터를 들고있다고 했는데 이걸 조금더 쉽게 이해하면 **노드들을 계층적으로 관리한다고 생각하면 된다.**
- 가장 아래 계층(0)에는 모든 노드를 들고 있고 상위 계층으로 올라 갈 수록 적은 노드를 들고 있다. 각 계층의 노드들은 서로 다른 HEAD를 들고 있을 확률이 존재하고, 각 노드들의 다음 노드도 다르다.
- Skip list의 이러한 계층화 검색 구조를 HNSW에 들고와서 NSW를 계층화 시킨다.

## HNSW(HNSW - Hierarchical Navigable Small World graphs)

![img](https://pangyoalto.com/content/images/2023/09/Screen-Shot-2023-07-29-at-4.45.45-PM.png)

- **HNSW는 NSW와 Skip list의 색심 아이디어를 합친 그래프 구조이다.**
- NSW의 단점은 정답과 거리가 꽤 먼 Local Minimum에 빠질 수 있다는 것이다.
- 하지만 Entry point를 가장 높은 차원의 노드를 기준으로 검색을 시작하면 문제를 어느정도 해소할 수 있다.
- **HNSW의 핵심하이디어는 결국 NSW를 계층화 하여 egde를 길이에 따라 다른 계층으로 분류하고, 이를 통해 네트워크 크기에 어느정도 독립적인 edge를 평가할 수 있게 되어 로그 스케일의 확장이 가능해진다는 점이다.**
- HNSW는 계층을 나눌 때 가장 긴 edge를 최상위 계층에 배치하며 시작한다. 각 계층에서 greedy 한 방식으로 계속 데이터를 탐색하는데 여기서 local minimum 상태에 도달하면 하위 계층으로 내려간다.
- 이러한 과정을 반복하면서 모든 계층에 노드당 되채 edge 개수를 제한, 즉 클러스터링 계수를 제한하면 HNSW의 검색을 로그 스케일로 수행 할 수 있다. 

![img](https://pangyoalto.com/content/images/2023/09/1_ziU6_KIDqfmaDXKA1cMa8w.webp)

- HNSW도 NSW처럼 여러 Entry point를 사용하면 검색 정확도가 올라간다. 그래서 HNSW는 하이퍼 파라미터로 Entry point를 조절이 가능하다.

### HNSW 그래프 생성

- HNSW 생성시 각 노드는 자신의 레이어 레벨을 할당 받는다.
- 논문에서 레이어의 레벨을 결정하는 수식은 **⌊-ln(unif(0..1))∙mL⌋**로 정의 된다고 한다.
- mL은 하이퍼 파라미터로 edge의 개수를 M이라고 했을 때 1/ln(M)이 최적 값이라고 논문에선 설명한다.
- 레이어 레벨 계산식에 로그가 씌어져 있어 상위 레벨로 올라갈 수록 노드의 개수는 기하급수적으로 감소하고, 대부분의 노드 레벨은 제일 최하층에 배치된다.
- 노드가 레벨을 할당 받으면 삽입은 두 phase로 나뉘어 진다.
  - 상위 레이어로 부터 출발해 greedy하게 가까운 정점을 찾는다. 찾은 정점은 다음 레이어에서 엔트리 포인로 활용된다. 할당 받은 레이어 레벨에 도착하면 두 번째 phase로 넘어간다.
  - 정점을 현재 레이어에 넣고, 이후 설정한하이퍼 파람터 개수만큼 가까운 이웃을 찾고 edge를 생성한다. 이러한 작업을 하위 레이어에서도 반복하고 최하층 레이어에 까지 삽입을 완료하면 알고리즘은 멈춘다.

![img](https://pangyoalto.com/content/images/2023/09/1_jEGA6ZXYR0qwgrtIEJ9vWg.webp)

- 위 그림은 HNSW에서 레이어 레벨이 2이고 edge의 개수가 2인 정점이 입력 후 연결되는 과정을 나타낸 그림이다.
- 하위 레이어에 더 가까운 정점이 많이지므로 edge가 레이어 마다 조금씩 달라지게 된다.

### HNSW와 Skip list

- HNSW 계층화는 Skip list가 Linked list 들을 여러 레이어로 관리하는것과 비슷한다.
- Skip list와 HNSW 모두 i 번째 레이어에 등장하는 원소는 i+1 번째 래이어에 확률 p로 등장하므로 상위 에이러로 갈수록 원소의 개수는 기하급수적으로 감소한다.

#### HNSW와 IVF(invert File Index)

![img](https://pangyoalto.com/content/images/2023/09/1_3Mm9lL73jscwxvDNe8_WzA.webp)

- 지금까지 설명한 방식은 모든 노드를 기준으로 설명을 했지만 Faiss에선 여기에 IVF 방식을 적용할 수 있다.
- IVF의 정석적인 방식인 보르노이 다이어 그램의 보르노이 대푯값을 사용하여 HNSW의 노드를 구성한다.
- 그럼 결국 메모리에 저장해야할 노드의 갯수가 줄어들어 검색 속도가 빨라진다.
- 이 방식은 기존 IVF 방식과 쌩 으로 HNSW를 사용할 때 보다 서로의 단점을 잘 보완해준다.
  - IVF 방식은 항상 trade-off를 해야하는데 보르노이 대푯값을 많이 만들때 데이터 셋이 크다면, cell에 속하는 벡터의 수가 많아져 brute force 검색시 시간이 너무 오래걸린다. 즉 쿼리 벡터와 직접 거리를 계산할 후보 벡터가 늘어난다.
  - HNSW도 데이터 셋이 크면 너무 많은 층을 많들어야 한다. 이러면 당연히 메모리에 부하가 온다.
- 그래서 두 방식을 결국 합치면 **데이터가 많아져도 빠르게 계산이 가능하여  cell에 속하는 벡터의 수를 적게 설정이 가능하고 대푯값의 수를 크게 가져 갈 수 있다.**

> Referense
>
> https://www.pinecone.io/learn/series/faiss/hnsw/
>
> https://jerry-ai.com/30
>
> https://ohgym.tistory.com/10
>
>  https://opentutorials.org/module/1335/8821
>
> https://pangyoalto.com/faiss-1-hnsw/