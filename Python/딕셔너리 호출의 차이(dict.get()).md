#Python #딕셔너리
## 두 개의 다른점
`dict['keyname']`과 `dict.get('keyname')`의 차이는 주로 **키가 없을 때의 동작**과 **기본값 지정** 여부에 있다.
- 예외 발생 여부
    - `dict['keyname']`
        - 해당 키가 없으면 `KeyError`가 발생합니다.
        - 키 존재가 확실할 때만 사용해야 안전합니다.

    - `dict.get('keyname')`
        - 키가 없으면 예외를 일으키지 않고 `None`을 반환합니다.
        - 프로그램이 멈추지 않고 계속 실행됩니다.

- 기본값 지정    
    - `dict.get('keyname', default_value)`
        - 키가 없을 때 두 번째 인자인 `default_value`를 반환하도록 할 수 있습니다.
        - 예: `my_dict.get('age', 0)` → 키가 없으면 `0` 반환

    - `dict['keyname']`은 기본값 지정 기능이 없습니다.
        
- 사용 예시
    ```
```python
data = {'name': 'Alice', 'age': 30}

# 1) dict[...] 사용
print(data['name'])  # 'Alice'
print(data['height'])  # KeyError 발생

# 2) get(...) 사용
print(data.get('name'))         # 'Alice'
print(data.get('height'))       # None (예외 없음)
print(data.get('height', 170))  # 170 (default)

```
- 언제 쓰면 좋은가
    - **`dict['key']`**:
        - 해당 키의 존재가 확실하거나, 키가 누락되면 코드를 바로 멈추고 오류 원인을 파악하고 싶을 때.
    - **`dict.get('key', default)`**:
        - 키가 없을 가능성이 있고, 그럴 때 `None`이나 지정한 기본값으로 처리하면서 유연하게 넘기고 싶을 때.
이처럼 두 방식은 에러 처리 방식과 기본값 지정 가능 여부에서 차이가 있으니, 상황에 맞춰 선택해서 사용하시면 됩니다.