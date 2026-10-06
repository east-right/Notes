#알고리즘 #자료구조 
## 스택
- 스택은 한쪽 끝으로만 자료를 넣고 뺄 수 있는 자료 구조를 의미한다.
- 간단하게 말해서 **"선입후출"형의 자료구조를 가지고 있다고 생각하면 된다.(선입선출 반대)**
- 가장 간단한 예시는 빨래통을 생각하면 쉽다.
	- 가장 먼저 들어간 빨랫감은 제일 아래에 위치한다.
	- 가장 나중에 들어간 빨랫감은 제일 위에 위치한다.
	- 나중에 들어온(위에 있는) 빨랫감을 덜어내지 않으면 먼저 들어온(아래에 있는) 빨랫감을 꺼낼 수 없다.
- 스택형 자료구조는 다른 말로 LIFO(Last In First out)이라고 부른다.
![](assets/Pasted%20image%2020250304203441.png)
- 스택의 **구현**은 [연결리스트](알고리즘_2주차-연결리스트(LinkedList).md)의 **반대 형식으로 구현 된다**라고 생각하면 편하다.
	- 연결리스트
		- 초기 값(node1)이 들어오면 head에 배정, node1의 next는 None
		- 신규 값(node2)이 들어오면 head는 고정, node1의 next는 node2가 되고, node2의 next는 None 
		- 신규 값(node3)이 들어오면 head는 고정, node2의 next는 node3가 되고, node3의 next는 None
		- head.next.next.val => node3.val
	- 스택
		- 초기 값(node1)이 들어오면 top에 배정, node1의 next는 None
		- 신규 값(node2)이 들어오면 top에 배정, node2의 next가 node1
		- 신규 값(node3)이 들어오면 top에 배정, node3의 next가 node2
		- top.next.next.val => node1.val
> 여기서 가장 중요한 점은 LinkedList와 Stack이 **개념이 반대되는 용어들이 아니란거다. 코드의 구현이 반대가 된다는 것이다.**
> 정확히 반대되는 개념은 다음에 배울 큐(Queue)라는 알고리즘 이다.
