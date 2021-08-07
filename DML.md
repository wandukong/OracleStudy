# DML

## SELECT : 레코드 검색
```sql
select distinct | * | 컬럼명 as 별칭, 컬럼명 별칭,....
from 테이블이름
[where 조건식 ]
[order by 컬럼명 desc | asc , .. ];
 ```
 
##### 컬럼명 지정
```sql
select empno "사원번호", name as 성명, position 직급, pay 급여 from emp2;
   ```
   - as 생략 가능, **큰 따옴표**로 묶기
   
##### || : 컬럼 데이터 붙이기
```sql
select name || position "이름+직급", pay 급여 from emp2;
```
```
나사장대표이사	100000000
전부장부장		72000000
최일도과장		50000000
```

##### distinct : 고유 데이터 출력
```sql
select distinct position from emp2;	
```


## UPDATE : 레코드 수정
```sql
update 테이블이름 set 컬럼명=변경값, 컬럼명=변경값, 컬럼명=변경값, ..... [ where 조건식 ]
```
```sql
update de_test set gender='M'; -- 전체 레코드 수정
update de_test set qq=100 where name='ss'; -- 조건에 맞는 레코드 수정
update de_test set gender='M', hobby='잠자기', name='너사원' where name='나사장'; -- 여러 필드 수정
```

## DELETE : 레코드 삭제
```sql
delete from 테이블이름; -- 전체 레코드 삭제
delete from 테이블이름 where 조건; -- 조건에 맞는 레코드 삭제
```
```sql
delete de_test where name='ss';
delete de_test where addr is Null;
```