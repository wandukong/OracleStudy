
# DDL

## ⚽CREATE : 테이블 생성
```sql
create table 테이블이름(
	컬럼명 datatype [ null | not null ] [ constraint 별칭 제약조건 ] ,
	컬럼명 datatype [ null | not null ] [ constraint 별칭 제약조건 ] ,
	.....
)
```
```sql
CREATE TABLE emp 
(	empno NUMBER(4)	constraint emp_empno_pk primary key,
	ename VARCHAR2(10),
	job VARCHAR2(9),
	deptno NUMBER(2) constraint emp_deptno_fk references dept(deptno)
);
```

### 테이블 복사
- **제약 조건**은 복사되지 않는다. (null | not null은 복사됨)
- 형식만 복사하고 싶은 경우 where절에 조건이 합당하지 않게 작성한다. ex) where 1=0
```sql
create table 테이블 as 복사할 테이블 내용;
```
```sql
create table dept_copy as select * from dept where deptno = 1000;
```

## 🏈ALTER : 테이블 수정

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

## ⚾DROP : 테이블 삭제
```sql
drop table 테이블이름;
```

## 🎱TRUNCATE : 테이블 모든 데이터 삭제
- 테이블이 최초 생성되었을 당시의 storage만 남기고, 데이터가 담겨 있던 storage는 Release된다. 
- CREATE TABLE을 한 직후의 상태로 만든다.
- 조건 사용 X
- rollback X (DML 명령어가 아니다)
```sql
truncate table 테이블이름;
```
```sql
truncate table de_test; 
```

## 🏀RENAME : 테이블 이름 변경
```sql
rename emp_old to emp_new;
```