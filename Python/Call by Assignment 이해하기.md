#파이썬 #객체 #가변 #불변 #callbyvaleu #callbyassigment #callbyreference 
> [가변 객체와 불변 객체와 복사 차이](obsidian://open?vault=Obsidian_note&file=Area_of_responsibility%2FPython%2F%EA%B0%80%EB%B3%80%20%EA%B0%9D%EC%B2%B4%EC%99%80%20%EB%B6%88%EB%B3%80%20%EA%B0%9D%EC%B2%B4%EC%99%80%20%EB%B3%B5%EC%82%AC%20%EC%B0%A8%EC%9D%B4)
> 이 내용이 베이스가 되는 내용이다.

- 크게 어려운 내용은 아니다. 앞의 내용을 공부하다 보면 자연스럽게 깨닫는 내용이다.

### Call by Value
```python
def swap(a, b): 
	a, b = b, a
	print(a, b)

n, m = 10, 20
swap(n, m)
print(n, m)
```
- 불변(immutable) 객체 기준으로 함수에 인자를 넘기면서 실행하면 `n,m`은 `10,20` 으로 나오고 `a,b`는 `20,10`으로 나온다.
- 즉 객체가 인자로 넘어갈 때 값으로 넘어가는 있는 call by Value 처럼 보인다.

### Call by Reference
```python
def swap(lst):
	lst[0], lst[1] = lst[1], lst[0]
	print(lst[0], lst[1])

lst = [10, 20]
swap(lst)
print(lst[0], lst[1])
```
- 위 처럼 가변 객체 기준으로 함수를 실행하면 함수 안 print도 `20,10` 밖 print도 `20,10`이 나오게 된다.
- 이는 마치 객체가 인자로 넘어갈 때 메모리 주소로 들어가는 call by Reference 처럼 보인다.

즉 파이썬에선 C++과 달리 **함수에 인자를 넘겨 줄 때 값 그 자체를 넘겨줄지 아니면 메모리 주소를 넘겨줄지 함수에서 명시적으로 지정할 수 없다.**

### Call by Assignment
- 파이썬은 함수에 인자를 넘겨 줄때 Call by Assignment를 사용한다.
- 파이썬은 모든 것이 객체이다. 즉 **변수에 어떠한 값을 할당할 때 실제 값들은 변수가 아니라 객체가 생성이 되고 변수는 그 객체를 가르키는 것 이다.**
- 함수에 의해서 호출된 인자는 모두 call by reference 형태로 들어온다. 하지만 함수 안에서 레퍼런스가 가르키는 객체를 조작할 경우, 그 레퍼런스가 가르키는 객체의 변경가능여부(가변, 불변)에 따라 동작 방식을 결정한다는 것이다.
	- 가변이면 바꿀수 있으니 가르키는 메모리 주소를 안고치고 메모리 안의 값을 고침
	- 불변이면 메모리 값을 고칠수 없으니 새 메모리를 할당받고 객체가 그 메모리를 가르침

![](assets/Pasted%20image%2020260425194525.png)
- a가 불변 변수 일 땐 인자로 들어가 변형이 일어나면 새롭게 메모리가 할당이 되지만(id 다름)
- a가 가변 변수 일 땐 인자로 들어가 변형이 일어나면 메모리의 값이 변경이 된다.(id 같음)