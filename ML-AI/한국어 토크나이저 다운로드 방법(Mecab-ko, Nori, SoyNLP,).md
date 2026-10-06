#mecab #Nori #Tokenizer

# mecab-ko
와 진짜 고생했다. 가장 쉬운 방법을 알려준다.
```
pip install python-mecab-ko
pip install python-mecab-ko-dic
```
을 입력하면 된다. 
 **이렇게 다운로드 한 패키지는 konlpy.tag가 아니라**  아래처럼 mecab으로 불러와야한다.
 ```python
 from mecab import MeCab 
 mecab = MeCab() 
 print(mecab.morphs("한국어 토크나이저 테스트입니다."))
```

## Nori
이것도 진짜 고생했다. 아래의 방법으로 다운로드 한다.
```
pip install nori-clone
```

아래의 코드로 예시코드를 작동한다.
```python
import nori, os

# 1) Dictionary 객체 생성 및 사전(.nori) 로드
package_dir = os.path.dirname(nori.__file__)
dict_path = os.path.join(package_dir, "dictionary", "latest-dictionary.nori")

dictionary = nori.Dictionary()
dictionary.load_prebuilt_dictionary(dict_path)

# 2) NoriTokenizer 생성 시 꼭 dictionary 인자 전달
tokenizer = nori.NoriTokenizer(dictionary)

# 3) 토큰화
result = tokenizer.tokenize("이 프로젝트는 nori 토크나이저 테스트입니다.")
print([token.surface for token in result.tokens])

```
일단 제일 중요한 부분은 **load전 무조건 `dictionary = nori.Dictionary()`로 사전 리스트를 불러와야 한다.**
그리고 그 path를 맞춰 줘야한다. 자동으로 안잡아 주는거 같다.
이렇게 사전 리스트 path를 들고 오고 토크나이저를 불러와야한다.
## SoyNLP
```
pip install soynlp
```
이건 쉽다

## Kiwi
```
pip install kiwipiepy
```
이것도 쉽다
