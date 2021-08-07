
## 테이블 생성
```sql
create table 테이블이름(
	컬럼명 datatype [ null | not null ] [ constraint 별칭 제약조건 ] ,
	컬럼명 datatype [ null | not null ] [ constraint 별칭 제약조건 ] ,
	.....
)
```

## 제약 조건

### unique
> **유일한 값**을 삽입하도록 한다. 
- 중복 안됨. (유일한 값)
- null 허용 (만약, not null 지정하면 null 허용 안됨)
- 테이블을 만들 때 pk와 같은 효과를 주기 위한 제약 조건이다.
- 한 테이블에 여러 컬럼에 사용 가능하다.
```sql
CREATE TABLE userlist(
	id VARCHAR2(10) CONSTRAINT id_pk PRIMARY key,
	jumin char(13) not null CONSTRAINT jumin_un unique
);
insert into userlist2 values('aa', '111');
insert into userlist2 values('bb', '222');
insert into userlist2 values('dd','111'); -- 위배
```



### check
> **조건**을 주어 조건에 만족하는 값만 삽입 가능하도록 한다.
```sql
create table ck_test(
	name varchar2(10) not null,
	age number(2) check(age between 20 and 30)
)
insert into ck_test values('dd',30);
insert into ck_test values('gg',50); -- 위배
insert into ck_test values('hh',18); -- 위배
insert into ck_test values('dd',22); 
```


### default
 > **기본값**을 지정하여 값을 넣지 않아도 기본값이 들어가도록 한다.
- null 허용 시, default 컬럼의 값을 생략하면 무조건 default가 들어간다.
- null 입력하고 싶은 경우, 직접 null을 넣어야 한다.
```sql
create table de_test(
    name varchar2(10) not null,
    addr varchar2(10) DEFAULT '서울'
);
insert into de_test values('pp','제주');
insert into de_test(name) values('oo'); -- addr에 '서울'이 입력된다.
insert into de_test values('ss', default);
insert into de_test values('ss', null);
select * from de_test;
```

## 테이블 수정

### 컬럼 추가
```sql
alter table 테이블이름 add (컬럼명 자료형 [제약조건] , 컬럼명 자료형 [제약조건] , ....);
```
- 추가하려는 필드가 2개 이상일 경우 **괄호**로 묶기
```sql
alter table de_test add gender char(2);
alter table de_test add (qq number, zz varchar2(02)); 
```
### 컬럼 삭제
```sql
alter table 테이블이름 drop column 컬럼이름;
```
- 삭제하려는 필드가 2개 이상일 경우 **괄호**로 묶기
```sql
alter table de_test drop column gender;
alter table de_test drop (qq,zz);
desc de_test
```

### 컬럼 data type 변경
```sql
alter table 테이블이름 modify 컬럼이름 변경자료형;
```
- 변경 원하는 필드에 레코드가 없어야 한다.
 ```sql
alter table de_test modify gender number; 
desc de_test;
```

### 컬럼 이름 변경
```sql
alter table 테이블이름 rename column 기존컬럼명 to 변경컬럼명;
```
```sql
alter table de_test rename column qq to score;
alter table de_test rename column zz to grade;
desc de_test;
```

## 테이블 삭제
```sql
drop table 테이블이름;
```
## DML

### 1. SELECT : 레코드 검색
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
select  name || position "이름+직급", pay 급여 from emp2;
```
```
나사장대표이사	100000000
전부장부장		72000000
최일도과장		50000000
```

##### distinct : 고유 데이터 출력
```sql
select  distinct position from emp2;	
```


### 2. UPDATE : 레코드 수정
```sql
update 테이블이름 set 컬럼명=변경값 , 컬럼명=변경값 , 컬럼명=변경값 , ..... [ where 조건식 ]
```
```sql
update de_test set gender='M'; -- 전체 레코드 수정
update de_test set qq=100 where name='ss'; -- 조건에 맞는 레코드 수정
update de_test set gender='M', hobby='잠자기', name='너사원' where name='나사장'; -- 여러 필드 수정
```

### 3. DELETE : 레코드 삭제
```sql
delete from 테이블이름; -- 전체 레코드 삭제
delete from 테이블이름 where 조건; -- 조건에 맞는 레코드 삭제
```
```sql
delete de_test where name='ss';
delete de_test where addr is Null;
```
```sql
truncate table 테이블이름;
```
- 모든 데이터 삭제, 조건 사용 X
- rollback이 안된다. (DML 명령어가 아니다)
```sql
truncate table de_test; 
```


### ROLLBACK
> 이전 상태로 되돌리는 것을 말한다.
- DML 명령어 처리에만 적용된다. DCL X, DDL X



## 연산자 

**산술 연산자**  
> +,  -,  *,  /,  mod(값, 나눌 수) 
	
```sql
select mod(7,3) from dual;
```
 <hr />
 
**관계 연산자**
> \> , <, >=, <=, !=, =
 <hr />
 
**조건 연산자**
 > and, or, not, 컬럼명 in (값, 값, ..), 컬럼명 between 최소 and 최대
 ```sql
select * from emp2 where position in ('과장', '부장');
select * from emp2 where deptno not in ('1000', '1005', '1011');
```
<hr />

> 컬럼명 like '와일드카드문자열' 
 - _:한 글자, %:0개 이상
```sql
select name, id from professor where id like '%-_'; -- only-u
```
<hr />	 

> 컬럼명 연산자 any(값1, 값2, ...)
- 검색 결과와 하나 이상이 일치하면 참 
```sql
select * from professor where pay > any(200,300,400);
```
<hr />

> 컬럼명 연산자 all(값1, 값2, ...)
- 검색 결과와 모든 값이 일치하면 참 
```sql
select * from professor where pay > all(200,300,400);
```
<hr />

**null 연산자**
> is null, is not null, nvl(값, 대치값)
- 값에 null 이있으면 대치값으로 변경
```sql
select nvl(bonus,0), nvl(bonus+200,200) from professor;
 ```
 <hr />

## 함수

**집계 함수**

> sum(컬럼명), avg(컬럼명), max(컬럼명), min(컬럼명)

> count(컬럼명)
- 총 레코드수(null 값 제외)
> count(*)
- null을 포함한 총 레코드수
> rank

 <hr />