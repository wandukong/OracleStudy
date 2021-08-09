
# join
> 두 개 이상의 테이블들을 연결 또는 결합하여 데이터를 출력하는 것을 말한다.

## 🛴inner join
> 기준 테이블과 조인 테이블 모두 데이터가 존재해야 조회할 수 있다.
```sql
select 컬럼명 , .....
from 테이블이름 별칭 [Inner] join 테이블이름 별칭
on 별칭.컬럼명 = 별칭.컬럼
```
```sql
-- 학생의 담당 교수 찾기
select s.name, p.name, p.deptno 
from student s join professor p on s.profno = p.profno;
```
#### 여러 개 조인 
```sql
select 컬럼명 , ..... from 테이블이름 별칭 
[Inner] join 테이블이름 별칭 on 별칭.컬럼명 = 별칭.컬럼명 
[Inner] join 테이블이름 별칭 on 별칭.컬럼명 = 별칭.컬럼명 ...
```
```sql
-- 학생의 담당교수 찾고, 학과명도 찾기
select s.name, p.name, p.deptno, d.dname
from student s join professor p on s.profno = p.profno
join department d on p.deptno = d.deptno;
```

## 🚗outer join
#### left outer join
> 테이블의 조인 조건이 맞는 경우 각 테이블에서 해당 데이터를 가져오고, 조건이 맞지 않는 경우 각 테이블의 레코드 중 left 테이블의 데이터는 가져오고, right 테이블의 컬럼들은 null로 채운다.  
 
```sql
select 컬럼명, ..... 
from 테이블이름 별칭 left outer join 테이블이름 별칭 on 별칭.컬럼명 = 별칭.컬럼명
```
```sql
select e.ename, job, d.loc, e.deptno
from dept d left outer join emp e on d.deptno = e.deptno;
```
<hr />

#### right outer join
> 테이블의 조인 조건이 맞는 경우 각 테이블에서 해당 데이터를 가져오고, 조건이 맞지 않는 경우 각 테이블의 레코드 중 right 테이블의 데이터는 가져오고, left 테이블의 컬럼들은 null로 채운다.  
```sql
select 컬럼명, ..... 
from 테이블이름 별칭 right outer join 테이블이름 별칭 on 별칭.컬럼명 = 별칭.컬럼명
```
```sql
select e.ename, job, d.loc, e.deptno
from dept d right outer join emp e on d.deptno = e.deptno;
```
<hr />

#### full outer join
> 테이블의 조인 조건이 맞는 경우 각 테이블에서 해당 데이터를 가져오고, 조건이 맞지 않는 경우 각 테이블의 레코드를 가져오고, 나머지 부분은 null로 채운다.

  
```sql
select 컬럼명, ..... 
from 테이블이름 별칭 full outer join 테이블이름 별칭 on 별칭.컬럼명 = 별칭.컬럼명
```
```sql
select e.ename, job, d.loc, e.deptno
from dept d full outer join emp e on d.deptno = e.deptno;
```