#추천시스템 #알고리즘 #자료구조 
## Hashing(헤싱) 이란

- 헤싱 자체는 간단하게 생각하면 Python의 Dictionary 라고 생각하면된다.
- 해싱은 **해시함수를 통해 특정한 값으로 추출하는 것을 의미한다.**

<img src="https://velog.velcdn.com/images/bbaekddo/post/6045ef32-ade1-4fd8-82d5-e85d1bbeb5bb/image.png" alt="img" style="zoom:50%;" />

> 출처: https://velog.io/@bbaekddo/cs-3

- 위와 같이 데이터(위 사진은 텍스트)가 들어오면 해시 함수를 통해 특정 값으로 데이터가 변환되어 나오는 걸 의미한다.
- 해시 함수란 **임의의 길이를 갖는 임의의 데이터를 고정된 길이의 데이터로 매핑하는 단방향 함수를 말한다.**
- 여기서 제일 중요한건 **단방향 함수라는 점이다. 인풋 데이터가 해시 함수에 들어가서 나온 결과 데이터는 반대로 넣어도 인풋 데이터가 나오지 않게 만든다는 점이다.**
- 해싱은 key와 Value로 이루어져 있다.
  - Key: Value의 주소 역할을 한다. 이 개념이 되게 애매모 하지만 보통 인풋 데이터 들이 해싱의 key 역할을 담당한다. Key는 중복되지 않고 해시 함수를 통해 Value 값이 나오면 그 값에 매핑(Mapping) 한다.
  - Value: Value는 해싱 함수을 통해 나온 결과 값을 의미한다.  Value 값으론 Key값을 예측할 수 없으며 하나의 Value가 두 개의 key를 가질 수 있다.
- 해시 함수는 아래와 같은 상황에서 많이 사용된다.
  - 다양한 자료 구조
    - 해시 테이블 또는 해시 맵이라는 형태로 O(1)이라는 시간 복잡도로 접근하는데 사용
    - 특정 Key를 해싱해서 나오는 문자열 Value(이게 결과) 
  - 프로그래밍 언어에서 제공되는 해시 함수
    - Java: Has Map
    - Javascripts: 객체 or Map
    - Python: **사전(Dictionary)**
  - 암호화
    - 입력값을 해싱핼을 때 출력값은 일정하다는 것을 근거로, 사용자의 비밀번호나 중요 정보의 내용을 해싱하여 복호화 할 수 없게 만드는 것이다.
- 해싱 기법
  - 자료 구조
    - 제산법: 버킷 주소(인덱스) = Key % 버킷 크기
    - 중간 제곱법: Key 값을 제곱한 후 결과 값의 중간 부분에 있는 비트만 선택해서 비컷 주소로 사용

  - 보안
    - MD5: 충돌 회피성의 문제로 현재는 사용중지
    - SHA: 현재 가낭 많이 사용되는 해싱 암호화

- 본 글에서의 해싱은 MInHash를 설명하기 위한 개념으로 설명하고 있다. 즉 여기서 중요하게 여기는건 해싱이 암호화니 뭐히 하는게 아니라 **해싱 함수라는 걸 통해 이 함수에 key가 들어오면 value가 나오고 그걸 key에 mapping 한다.**는 개념이 중요하다.

## MinHash

- MinHash는 데이터 마이닝 분야에서 문서와 같은 자료형 데이터간의 유사도를 빠른 시간 내에 구하는 방식이다.
- 기본적으로 **완벽하게 거리를 재서 완벽히 비슷한걸 찾는 방식이 아닌 빠르게 근사하여 비교하는게 주 목적이다.**
- 즉 정확도 보단 속도에 초점을 둔 방식이다.

- Minhash 알고리즘은 차원을 줄여서 줄어든 차원의 정보 만으로 클러스터링 하였을 때 본래 데이터의 클러스터링 결과와 거의 비슷하도록(근사) 하는것이다.
- 본래 데이터의 차원이 너무 많거나 샘플의 수가 너무 많을 때 사용된다. 샘플 데이터의 정보가 너무 많아 계산 시간 혹은 로드 시간이 너무 길어 이러한 시간을 줄여주는 용도로 사용된다.
- 본 알고리즘 설명은 단어 단위 토큰 단위로 설명한다. **보통 N-gram단위로 토큰을 설정하지만 쉬운 이해를 위해서 단어 단위 토큰으로 설명한다.**
- 아래의 그림처럼 3개의 문서가 각각 4개의 단어를 가지고 있다.

![img](https://blog.kakaocdn.net/dn/mVDBb/btrsrPKuUr5/V2LnaSrUIPouGRUGF4Ttc1/img.jpg)

> 출처: https://jimmy-ai.tistory.com/117

- 각 문서의 단어 등장 여부를 나타내는 TF 행렬을 생성한다.
- 이를 기반으로 Jaccard Similarity와 MinHash를 비교한다.

### Jaccard Similarity

- 주로 TF 행렬간의 문서간의 유사도는 자카드 유사도를 사용한다.
- 자카드 유사도는 **모든 등장 아이템 중 서로 몇개가 겹치는지 체크하는 방식이다.**

$$
Similarity = \frac{n(A \cap B)}{n(A \cup B)}
$$

- 식은 위와 같으며 **교집합 원소 개수/합집합 원소 개수**로 계산한다.

![img](https://blog.kakaocdn.net/dn/ccl3sJ/btrslWKFBxX/g4WkiSeCCfTQdJsEd3e4y1/img.jpg)

> 출처: https://jimmy-ai.tistory.com/117

- 위 와 같이 자카드 유사도를 사용하면 A와 B의 문서 유사도는 매우 높게 나온다. 하지만 B와 C는 유사도가 매우 낮게 나온걸 확인 가능하다.
- 이러한 자카드 유사도는 **데이터가 많아지면 많아 질 수록 계싼해야하는 양이 기하 급수적으로 늘어난다.**
- 문서가 하나만 추가 되도 3번만 비교하면 되는게 6번으로 늘어난다.
- 심지어 **문서가 길어 N-gram 토큰의 개수가 많아지면 더욱 곤란하다.**
- **여기서 토큰간 모든 등장 여부를 비교할 필요는 없고 정보량을 줄여 유사도를 근사하여 측정하는 방법이 필요하게 되고, 여기서 고안 된 방법이 MinHash이다.**

### MinHash Argorithm

- Minhash는 해시 함수를 사용해서 데이터를 차원 축소하고 근사 유사도를 구한다.
- Min-hash에서 사용되는 해시함수는 **모든 원소들이 1개씩 값에 정확히 Premutation Mapping**되는 함수를 사용한다. 위 사항만 지켜진다면 어떠한 함수를 사용해도 된다.

![img](https://blog.kakaocdn.net/dn/ciT1Ee/btrspKQtarE/Bouuqgggkmn0Ch1yXAyuh1/img.jpg)

> 출처: https://jimmy-ai.tistory.com/117

- Minhash 알고리즘에서 **해싱 함수는 행렬의 열 Index를 다른 숫자로 변경시키는 함수를 사용한다.**
- 본 그림에서의 해시 함수는 인덱스 x를 입력으로 받아 그 값을 key로 설정하고 특정 값을 더해 7의 나머지를 Valur로 받는 함수로 설정하였다.
- f1과 f2라는 해시 함수를 적용해서 나온 Value 값들은 위와 같다.
- 딕셔너리로 표현하자면 f1은 {0:3, 1:4, 2:5 ...}, f2는 {0:1, 1:3, 2:5...} 이렇게 변경되었다.
- 이렇게 **해시된 결과를 바탕으로 각 문서의 최소 인덱스를 구해주면된다. **
- 여기서 최소 인덱스란, **1로 등장한 토큰들 중 최소의 해시 값**을 말한다.

![img](https://blog.kakaocdn.net/dn/bIpjVD/btrssC5g1sF/eRE0FZJHGfY6uwC1wwpOeK/img.jpg)

> 출처: https://jimmy-ai.tistory.com/117

- 1로 등장한 토큰들 중 최소의 해시값이란 위 그림과 같이 아래의 설명을 보자
  - f1이라는 해시 함수를 통해 나온 Value를 index로 사용한다.
  - 이제 각각 A,B,C 에서 1이 최초로 등장하는 Index 번호를 확인 한다.
  - A는 f1이라는 해시 함수를 통과해서 나온 인덱스 0(bux)과 1(airplane)에 0이 기록 되어 있고 3(car) 에 가서야 최초로 1이 등장하기에 A는 3이라고 기록한다.
  - B는 인덱스 0(bus)에 바로 1이 등장하지 0이라고 기록한다.
  - C는 인덱스 0(bus)는 0이고, 1(airplane)에 최초로 1이 등장하므로 1이다. 
  - f2 해시 함수를 통해 index를 변환 시켰을 때도 똑같이 새로운 행렬을 만든다.
- 위와 같은 방법으로 여러 개의 서로 다른 해시 함수에 같은 과정을 적용한다.
- 이러한 과정을 거쳐서 만든 행렬을 **Signiture 행렬**이라고 한다.

![img](https://blog.kakaocdn.net/dn/HXVLF/btrsrmPqmF7/V4CNeGktlRi5prygKYgWpK/img.jpg)

> 출처: https://jimmy-ai.tistory.com/117

- 해시 함수를 통해 나온 결과중 서로 같은 결과를 가지는 비율을 계산한다. 이 비율이 두 문서의 유사도를 나타낸다.

- 적용결과 A와 B의 Minhashing 결과는 3/5가 나온다. **이는 자카드 유사도로 유사도를 구했을 때랑 결과가 같다. **
- 물론 우연이긴 하지만 min-hashing을 사용한 결과 값이 자카드랑 비슷하다는걸 알 수 있다.
- 결국 **해시 함수의 개수가 많아지면, 실제 측정된 유사도는 자카드 유사도와 거의 동일**해진다. **이를 이용하여 자카드 유사도와 근사하게 분석을 수행해볼 수 있다.**
- **너무 많은 개수의 해시 함수를 구성하여 사용하게되면 정확도는 올라가겠지만 당연히 수행시간의 이점은 사라진다.**
- 결국 min-hashig은 **토큰의 개수보다 훨씬 적은 개수ㅗ도 문서 간의 유사도 측정이 쉬워져서 많이 사용되는 방식이다.**
- 이를 통해 LSh를 알아보는데 LSH는 Min-hashing 알고리즘을 기반으로 하는 유사한 데이터 그룹을 찾아 내는 방식이다.

## LSH(Locaity-Sensitive Hashing)

- LSH는 Min-hashing을 기반으로한 근사 유사도 기법이다.
- Min-hash로 얻은 signiture 행렬을 가지고 추가적인 작업을 통해 더욱더 유사도 계산량을 줄이는 방법이다.

![img](https://blog.kakaocdn.net/dn/b4El1i/btrstKQP7Ow/jOjdXodUty7UUuI9R2ItBK/img.jpg)

> 출처: https://jimmy-ai.tistory.com/118

- 위와 같이 Min-hash를 통해 나온 **signiture행렬을 band 라는 단위로 쪼갭니다.**
- 위 사진은 각 band에 2개의 signiture 벡터들이 들어가 있습니다.
- LSH는 **Bucket Hashing이란 기술을 사용하는데 이는 LSH의 핵심 기술입니다.**

![img](https://blog.kakaocdn.net/dn/BQHvA/btrssvzgg1O/3w3KWFzp4lsEOgcINe9m5k/img.jpg)

> 출처: https://jimmy-ai.tistory.com/118

- **Bucket Hashing란 각 band 내의 signiture 조합을 bucket이라는 임의의 빈 공간에 hashing 하고 같은 각 band 내의 signiture 행렬를 비교하는 것을 의미한다.**
- 각 band마다 새로운 bucket을 만들어 hashing을 진행한다.
- band 2의 경우 **A와 B 문서는 signature의 조합이 모두** **0,** **0**으로 같은 signature 조합을 가지고 있어, 같은 bucket에 두 문서가 포함되어 있다. 
- 그에 반해, band1 의 경우 같은 signiture 조헙이 없어 모두 다른 bucket에 배정 되었다.
- 여기서 가장 중요한 점은 LSH는 **두 문서가 단 1개의 band에서라도 같은 bucket으로 해싱 되면 두 문서는 유사한 문서로 취급한다.**

![img](https://blog.kakaocdn.net/dn/co2pId/btrsrUTWPDT/HZDRsgYX6ybIWNXb6ty1gK/img.jpg)

> 출처: https://jimmy-ai.tistory.com/118

- 이러한 LSH는 분명 장점과 단점이 존재한다.
- 실무에선 비교해야할 문서가 많을 텐데 그 양이 10만개만 되어도 비교해야할 문서 pair의 조합 수는 50억가지이다.
- 문서의 개수만큼의 hashing은 단지 o(n) 시간만 소요되므로 **비교적 시간을 크게 줄일 수 있다.**
- 물론 단점은 **기존 방식들 보다 정확도가 떨어지는 것 이다.**
- 근사 알고리즘의 단점이긴한데, LSH는 band의 개수와 한 band 안에 들어가는 row를 직접 조절이 가능하다.
- band가 많아지면 그 안의 signiture의 개수는 줄고 band가 적어지면 그 안의 signiture 개수는 많아진다.
- band 내 signature가 많아지면 **유사한 문서 pair를 놓칠 확률이 높아지고,**band가 너무 많아지면 **비슷하지 않은 문서가 유사 데이터로 묶일 가능성**이 있다.
- 총 200갸의 signiture 중에서 유사도의 threashold가 0.7 이상의 문서 pair를 찾는 상황에 band를 20개, 그 안의 row 수를 10개로 놓으면 아래와 같다.
  - 유사도 0.7인 문서 쌍이 10개의 signature에서 동일한 값 : (0.7)^10 = 0.028
  - 20개의 band에서 해당 문서 쌍이 모두 다른 bucket 할당 : (1 - 0.028)^20 = 0.564
- 즉, 약 56.4%의 확률로 유사한 문서 쌍을 놓치게 되는 예시를 의미한다.**(False Negative)**
- 반면 threashold가 0.4의 유사도의 문서를 찾으면
  - 유사도 0.4인 문서 쌍이 10개의 signature에서 동일한 값 : (0.4)^10 = 0.0001
  - 20개의 band에서 해당 문서 쌍이 모두 다른 bucket 할당 : (1 - 0.0001)^20 = 0.998
- 과 같이 단 0.2%의 확률로만 오분류 된다는걸 알 수 있다.
- 이상적인 유사도 threashold 설정에 따른 분서 분류 확률 curve는 아래와 같다.

![img](https://blog.kakaocdn.net/dn/cfyjhW/btrswUSWdWK/KuoXUAdnFOXczKUZQVT6yK/img.jpg)

> 출처: https://jimmy-ai.tistory.com/118

- 결국 적절한 trade-off가 최적의 결과를 얻는 지름길이다.

> Reference
>
> https://velog.io/@bbaekddo/cs-3
>
> https://jimmy-ai.tistory.com/117
>
> https://lifemath.tistory.com/7
>
> https://jimmy-ai.tistory.com/118