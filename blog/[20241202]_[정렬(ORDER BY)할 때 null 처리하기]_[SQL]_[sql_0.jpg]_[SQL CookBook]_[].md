# 정렬할 때 null 처리하기

정렬하려고 할때, 필드가 null을 포함하는 경우 null 값을 어떻게 정렬할지 지정하는 방법이 필요합니다.

![null 처리_0](img/sql/sql_0.jpg)


### CASE 식을 사용하여 플래그를 수행합니다. 

값이 null일 때 CASE 식을 사용하여 플래그 flag를 수행합니다. 즉, 두 개의 값을 지니는 플래그를 갖는 겁입니다.(0과 1) 
하나는 null을 나타내고(0 값) 다른 하나는 null이 아닌 값(1 값)을 나타냅니다. 일단 이 플래그 열을 ORDER BY 절에 추가합니다. 
그러면 null이 아닌 값을 건드리지 않고 null 값을 처음에 정렬할지 또는 마지막으로 정렬할지 여부를 쉽게 제어할 수 있습니다. 

#### NULL이 아닌 COMM을 우선 오름차순 정렬하고, 모든 NULL은 마지막에 나타냄

MySQL
```
select ename, sal, comm
from (select ename, sal, comm,
case when comm is null then 0 else 1 end as is_null
from emp) x
order by is_null desc, comm;
```

![null 처리_1](img/sql/sql_1.jpg)


#### NULL이 아닌 COMM을 우선 내림차순 정렬하고, 모든 NULL은 마지막에 나타냄

MySQL
```
select ename, sal, comm
from (select ename, sal, comm,
case when comm is null then 0 else 1 end as is_null
from emp) x
order by is_null desc, comm desc;
```

![null처리_2](img/sql/sql_2.jpg)


#### NULL을 처음에 나타낸 후, NULL이 아닌 COMM은 오름차순 정렬

MySQL
```
select ename, sal, comm
from (select ename, sal, comm,
case when comm is null then 0 else 1 end as is_null
from emp) x
order by is_null, comm ;
```

![null처리_3](img/sql/sql_3.jpg)


#### NULL을 처음에 나타낸 후, NULL이 아닌 COMM은 내림차순 정렬

MySQL
```
select ename, sal, comm
from (select ename, sal, comm,
case when comm is null then 0 else 1 end as is_null
from emp) x
order by is_null, comm desc;
```

![null처리_4](img/sql/sql_4.jpg)