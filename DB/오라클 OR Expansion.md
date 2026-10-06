- or expansion이란 or 절로 인해 range 스캔으로 못하니 oracle이 자연스럽게 union all로 처리해서 range scan을 하게 실행계획을 조정하는걸 말한다.
```sql
select *
from tab a
where (cust = :cust or tel_no =:tel_no) 
```
- 위와 같이 존재하면 두 컬럼 전부 인덱스가 존재하거나 결합인덱스 여도  index range scan을 사용할 수 없다.
- or 조건과 관련된(l`ike %_%`등) 코드들은 인덱스가 존재하더라도 어느 한 시작 지점을 특정할 수 없어서 range 말고 full을 타버린다.
-  그래서 아래와 같이 변경하면된다.
```sql
select *
from tab a
where cust = :cust 
UNION ALL
select *
from tab a
where tel_no =:tel_no
```
- 이렇게 하면 만약 두 컬럼 전부 인덱스가 존재한다면 각각의 작업은 range index scan이 활성화 된다.
- 내 생각인데 아마 이정도는 oracle이 최신 버전이면 해주지 않을까 싶긴한데 무튼 내가 주로 만질건 과거 버전이니
- 혹은 아래와 같이 해도 된다.
```sql
select /*+ use_concat */ *
from tab a
where (cust = :cust or tel_no =:tel_no) 
```
- `use_concat`힌트를 사용하면 강제로 `union all`로 풀고 실행계획이 생성된다.
- 이거 사용하는게 편할듯, 코드 직관성도 좋고