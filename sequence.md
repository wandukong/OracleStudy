# sequence
 > 자동으로 순차적으로 증가하는 순번을 반환하는 데이터베이스 객체, 자동 증가 컬럼
 - 순차적으로 값을 증가하여 내부적으로 유일한 값을 만든다.
 - 시퀀스를 생성할 때 시작값, 최대값, 증가치를 지정하여 자동 증가 하도록 만든다.

## ✏시퀀스 생성
```sql
create sequence 시퀀스이름 -- 1부터 시작
	[start with 시작값 ] 
	[increment by 증가치 ] 
	[maxvalue 최대값 ] 
	[minvalue 최소값 ] 
	[cycle | nocycle ] 
	[cache | nocache ]
```

```sql
create sequence auto_num; -- 1부터 시작해서 1씩 증가한다.
```

```sql
create sequence seq_problem
	maxvalue 5
	cycle
	cache 2;
	
create table tbl_problem(
    id number,
    name varchar2(10)
);

insert into tbl_problem values(seq_problem.nextval, 'a');  	-- 1
insert into tbl_problem values(seq_problem.nextval, 'b');  	-- 2
insert into tbl_problem values(seq_problem.nextval, 'c');  	-- 3
insert into tbl_problem values(seq_problem.nextval, 'd');  	-- 4
insert into tbl_problem values(seq_problem.nextval, 'e');  	-- 5
insert into tbl_problem values(seq_problem.nextval, 'ㄱ');	-- 1
insert into tbl_problem values(seq_problem.nextval, 'ㄴ');	-- 2
insert into tbl_problem values(seq_problem.nextval, 'ㄷ');	-- 3
insert into tbl_problem values(seq_problem.nextval, 'ㄹ');	-- 4
insert into tbl_problem values(seq_problem.nextval, 'ㅁ');	-- 5
```

## 🖊시퀀스 값 증가
> 시퀀스이름.nextval

```sql
select auto_num.nextval from dual; 
```

## 🖍시퀀스 값 증가
> 시퀀스이름.currval
```sql
create sequence seq_test_no; 

insert into seq_test values(seq_test_no.nextval, 'aa'); -- no: 1
insert into seq_test values(seq_test_no.nextval, 'bb'); -- no: 2
insert into seq_test values(seq_test_no.nextval, 'cc'); -- no: 3
insert into seq_test values(seq_test_no.nextval, 'dd'); -- no: 4
```


## 🖌시퀀스 값 초기화
>  alter sequence 시퀀스이름 restart;   -- 1로 초기화
  
> alter sequence 시퀀스이름 restart with 정수;  -- 특정 수로 초기화