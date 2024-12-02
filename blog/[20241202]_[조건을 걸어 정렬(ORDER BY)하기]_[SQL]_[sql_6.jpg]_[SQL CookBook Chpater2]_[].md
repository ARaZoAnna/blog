# 데이터 종속 키 기준으로 정렬하기



ORDER BY만 사용한 데이터

```
select ename, sal, job, comm
from emp
order by comm, sal;
```

![정렬한 데이터](img/sql/sql_5.jpg)


### ORDER BY 절에서 CASE 식 사용


#### "JOB이 'SALESMAN'이면 COMM 기준으로 정렬하고, 그렇지 않으면 SAL 기준으로 정렬하고자 합니다."

```
select ename, sal, job, comm
from emp
order by case when job = 'SALESMAN' then comm else sal end;
```

![CASE를 사용한 정렬한 데이터](img/sql/sql_6.jpg)
