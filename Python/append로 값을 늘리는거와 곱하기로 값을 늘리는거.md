#Python 
이게 뭔소리냐 할거다 아래의 문제를 보자
```python
lst = [[2,1,1],[2,3,1],[3,4,1]]
lenght = len(lst) +1
# 1번경우
input_list = [[]] * lenght

# 2번 경우
input_list = []
for _ in range(lenght):
    input_list.append([])

# 로 아래의 코드를 돌리면 결과가 이렇게 나온다.
for i in lst:
    a, b, c = map(int, i)
    input_list[a].append((b, c))

   #1번 경우
   >>> [[(1, 1), (3, 1), (4, 1)], [(1, 1), (3, 1), (4, 1)], [(1, 1), (3, 1), (4, 1)], [(1, 1), (3, 1), (4, 1)]]
   #2번 경우
   >>> [[], [], [(1, 1), (3, 1)], [(4, 1)]]
```

지금 나는 2번 경우를 생각하고 코드를 만든건데 1번 경우 처럼 나와서 알아봤다.
분명 같은 `[]`를 늘리는 코드인데 결과가 차이가 많이난다.
   - 1번 경우 같은 경우 분명 특정 인덱스에 위치해 있는 리스트에 `.append`를 하였지만 모든 인덱스의 내부 리스트에 적용됨
   - 2번 경우 해당 인덱스에 존재하는 리스트에만 `.append`가 적용됨
그 이유를 찾아 보니 아래와 같다.
### 방식 1 (`input_list = [[]] * length`)
```python
input_list = [[]] * length
```
이 방식은 `input_list`에 빈 리스트(`[]`)를 `length` 개수만큼 넣는 방식입니다. 하지만, **이 방식은 문제가 있습니다.**  
리스트 복사 연산 (`*`)을 사용하면 같은 리스트 객체의 참조가 반복적으로 추가됩니다. 즉, **모든 요소가 같은 메모리 주소를 가리키는 동일한 리스트를 공유하게 됩니다.**

예를 들어,
```python
input_list = [[]] * 3
```
위의 코드를 실행하면, `input_list`는 다음과 같이 만들어집니다:
```python
input_list = [[], [], []]  # 겉으로 보기엔 다 다른 리스트처럼 보임.
```
하지만, 사실 내부적으로는 모든 요소가 **같은 리스트 객체를 참조하고 있습니다.** 즉, 하나를 변경하면 모두가 바뀝니다.
```python
input_list[0].append(1)
print(input_list)  # 출력: [[1], [1], [1]]
```
### 방식 2 (`input_list = []; for 문으로 추가`)
```python
input_list = []
for _ in range(length):
    input_list.append([])
```
이 방식은 `for` 루프를 이용해 매번 새로운 빈 리스트(`[]`)를 `input_list`에 추가합니다. 즉, 각각의 리스트는 **서로 다른 메모리 주소를 가지는 독립된 리스트 객체들입니다.**

예를 들어,
```python
input_list = []
for _ in range(3):
    input_list.append([])
```
위의 코드를 실행하면, `input_list`는 다음과 같이 만들어집니다:
```python
input_list = [[], [], []]
```
그리고 이 경우에는 각 리스트가 독립적입니다.
```python
input_list[0].append(1)
print(input_list)  # 출력: [[1], [], []]
```

### 문제의 원인: `input_list = [[]] * length` 사용으로 인한 참조 문제

만약 `input_list`를 첫 번째 방식으로 생성한 뒤 아래와 같은 코드를 실행한다면:
```python
for i in lst:
    a, b, c = map(int, i)
    input_list[a].append((b, c))
```
모든 `input_list`의 요소가 동일한 리스트를 참조하고 있으므로, 예를 들어 `input_list[0].append((b, c))`를 하면, `input_list[1]`, `input_list[2]`에도 동일하게 반영됩니다.

### 결론

- **`input_list = [[]] * length`는 동일 객체 참조 문제를 발생시키므로 사용하면 안 됩니다.**
    
- **`for` 루프를 사용하여 빈 리스트를 하나씩 생성하는 방식(`input_list = []; for _ in range(length): input_list.append([])`)이 올바른 방법입니다.**
