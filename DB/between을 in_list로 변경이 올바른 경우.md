#oracle #DB #dba #인덱스 
기본적으로 복합 인덱스 일때 accese하는 것을 기준으로 설명한다.
- between 조건은 in_list로 사용하면 효과 좋아지는 경우가 존재한다.
## between
- `where col 1 between 3` 이 기본형식이다.
- `where col >1 and col < 3` 이랑 동음의 어다.
- 기본적으로 범위를 지칭한다. 어떤 값들의 사이 범위를 구할때 사용한다.
- 등차(=)이 아닌 범위 기호이기에 복합 인덱스에서 선행 컬럼으로 들어왔을 때 후열 인덱스 조건이 =라도 index scan 범위가 늘어난다. 
```text
index = [col1 = [0,1,1,1,2,2,2,2,3,3,4], col2 = [a,a,a,b,a,b,b,b,c,a,b,a]]
일때 
select * from table
where col1 between 0 and 4
and col2 = a

면 선행 col1의 [1,1,1,2,2,2,2,3,3]을 전부 스캔하고 [a,a,b,a,b,b,b,c,a,b]에서 a를 필터링 해야한다.
즉 저기서 [*a*,*a*,b,*a*,b,b,b,c,*a*,b] 이렇게 찾아야 한다

```
**즉 between의 후속에 오는 찾고자 하는 값에 대한 조건절 인덱스의 거리가 멀면 매우 효과가 떨어진다.**
## in_list
- 만약 위 between을 in_list 행태로 코드를 다시 적으면 어떻게 될까?
```text
index = [col1 = [0,1,1,1,2,2,2,2,3,3,4], col2 = [a,a,a,b,a,b,b,b,c,a,b,a]]

select * from table
where col1 in (1,2,3)
and col2 = a
```
- 위와 같이 작성하면 1에서 a인거 찾고 나오고, 2에서 a인거 찾고 나오고, 3에서 a인거 찾고 나온다.
- 즉 위 between이 총 9 scan을 한다.(1전부, 2전부, 3전부)
- 하지만 in_list는 해당하는 거의 끝의 다음꺼 까지만 보기에 7scan으로 끝난다.
	- 만약 더욱 긴 인덱스면 그 차이는 더욱 눈에 띌것이다.
	- `1` = a개수 +1 = 3scan
	- `2`= a개수 +1 = 2scan
	- `3`= a개수 +1 = 2scan
	- 총 7scan
- 즉 `or expansion`과 같이 `union all`로 작성한 결과를 가진다.
```text
select * from table
where col1 = 1 
and col2 = a
union all
select * from table
where col1 = 2 
and col2 = a
union all
select * from table
where col1 = 3 
and col2 = a
```
이러면 매우 효과적이다!
## skip_scan 힌트
- skip_scan 힌트는 betweent 처럼  모두 스캔하는게 아니라
- 조건을 찾으면 건너 뛰면서 scan한다. 이건 찾아봐라 쉽다.
## between이 그래도 더 좋은 경우(in_list가 안 좋은 경우)
```
1. between 범위가 안클때
2. 찾아야 하는 값들이 멀리 떨어져 있지 않을 때
```
- 만약 `col1 between 1 and 100` 인거처럼 범위가 너무 넓으면 안쓰는게 좋다.
- index로 위치를 찾을 때 마다 `수직적 탐색` index scan을 진행하는데 in_list를 쓰면 위 작동 방식처럼 수직적 탐색이 98번 진행이 된다.
- 즉 브렌치 블록 index scan을 98번 반복을 해야하니 이때는 그냥 한번에 많이 찾고 filter 때리는 between이 더 좋다.
- 또한 찾아야 하는 값들이 멀리 떨어져 있지 않을 때 이다. 아래와 같은 경우다.
```text
index = [col1 = [0,1,1,1,2,2,2,2,3,3,4], col2 = [a,a,a,b,a,b,b,b,c,a,b,a]]

select * from table
where col1 in (1,2)
and col2 = a -> 인덱스 비효율
```
- 위와 같이 작성하면 이렇게 된다.
	- `1` = 3
	- `2` = 2
	-  총 5, 수평적 탐색 2
```text
index = [col1 = [0,1,1,1,2,2,2,2,3,3,4], col2 = [a,a,a,b,a,b,b,b,c,a,b,a]]

select * from table
where col1 between 1 and 2
and col2 = a -> 인덱스 효율
```
- 위와 같이 작성하면 이렇게 된다.
	- `1` = 3(전체 스캔한거임 2까지 가야하니깐)
	- `2` = 2(a다음 바로 b니)
	- 그 후 필터링 작업
	-  총 5, 수평적 탐색 *1*
- 즉 데이터 분포나 수직적 탐색 비용을 따져서 선택해야한다.
- 위와 같이 후열 조건에 만족하는 값이 between 사이에 가까이 있으면 가지고 오는 scan의 범위도 적을것이니 조금 포기하고 수평적 탐색 횟수를 줄이는게 더 좋다.
- 앵간히 많이 떨어져 있지 않으면 between이 더 낫다.