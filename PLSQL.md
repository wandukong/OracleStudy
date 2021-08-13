# PL/SQL
> PL/SQL은 오라클에서 제공하는 프로그래밍 언어이며 일반 프로그래밍 언어적인 요소를 다 가지고 있고 데이터베이스 업무를 처리하기 위한 최적화된 언어이다.  
 - 몇 개의 쿼리문 사이에 어떠한 연결 및 절차성이 있어야 하는 경우 사용할 수 없는 SQL문 단점을 극복하기 위해 절차적인 프로그래밍이 가능하다.
 - 모듈식 프로그램 개발 가능하다.

## 🏹선언부 (declare)
> 모든 변수나 상수를 선언하는 부분
> 
### 변수 선언
- **%type** : 필드명과 같은 타입 선언
- **%rowtype** : 테이블의 전체열을 갖는 타입(한 레코드)
```sql
declare 변수이름 [in] 데이터타입;   -- in 생략 가능
declare 변수이름 [in] 데이터타입:=값;
declare 변수이름 [in] 데이터타입 default 기본값;
declare 변수이름 [in] 테이블이름.필드명%type 
declare 테이블이름%rowtype
```
```sql
declare -- 동시에 선언 가능
	no number(4);
	irum varchar2(10):='sw';
    name varchar2(10) default '승완';
    name emp.ename%type;
    data emp%rowtype;
```
<hr />



## 🔫실행부 (begin ~ end)
> 제어문, 반복문, 함수정의 등의 로직을 기술함
```sql
begin 
	실행문;
end;
```
```sql
begin 
	dbms_output.put_line('sw'); --출력 
end;
```

**변수 사용**
- **into** : 테이블로부터 가져온 필드의 값을 변수에 저장하기 위해 select절에서 사용한다.
- **&** : 입력
```sql
declare data emp%rowtype;
begin 
    select * into data from emp where empno = 7839;
    dbms_output.put_line(data.ename || ', ' || 
					    data.job || ', ' || data.mgr || ', ' || data.deptno);
end;
```
```sql
declare 
    vno number(4);
    vname varchar2(20);
begin 
    select empno, ename into vno, vname 
    from emp 
    where empno = &empno; -- &empno 통해서 값 입력 받기
    dbms_output.put_line('입력값 : ' || vno || '<---->' || vname);
end;
```

## 💣조건문

### if

```sql
if 조건식 then 실행문장;
elsif 조건식 then 실행문장
..
else 실행문장;
end if;
```
```sql
declare
    su number :=90;
begin 
    dbms_output.put_line('program started. ');
    if su>=90 then dbms_output.put_line(su||'가 90보다 크거나 같다');
    elsif su>=80 then dbms_output.put_line(su||'가 80보다 크거나 같다');
    end if;
    dbms_output.put_line('program completed.');
end;
```
<hr />

### case
- 값을 **반환**한다.
```sql
case deptno 
    when 조건 then 결과
    when 조건 then 결과
    ...
    else 결과
end;
```
```sql
declare 
    ename emp.ename%type;
    deptno emp.deptno%type;
    dname varchar2(30);
    empno emp.empno%type;
    
begin
    select ename, deptno, empno into ename, deptno, empno from emp
    where empno = &empno;
    dname:=case deptno 
                when 10 then 'ACCOUNT'
                when 20 then 'RESEARCH'
                when 30 then 'SALES'
                when 40 then 'OPERATIONS'
            end;
	dbms_output.put_line( ename || ' ' || deptno || ' ' || dname);
end;
```


## 🔪반복문
**for**
```sql
for 변수이름 in (reverse) 시작..끝 loop -- reverse는 역순
    실행문장
end loop;
```
```sql
declare 
    dan number := &dan;
begin 
    for i in 1..9 loop
        dbms_output.put_line(dan || 'x' || i || '=' || dan*i);
    end loop;
end;
```

<hr />

**while**
```sql
while 조건식 loop
    실행문장;
    증감식;
end loop;
```
```sql
declare 
    su number := 0;
begin
    while su < 50 loop
        su := su + 1;
        dbms_output.put_line('현재 su 값은 ' || su || '이다.');
    end loop;
end;
```

<hr />

**loop**
```sql
loop
    실행문장;
    증감식;
    exit when 조건식;
end loop;
```
```sql
loop 
    fetch c_emptype into data;
    exit when c_emptype%notfound;
    dbms_output.put_line(data.name || ' ' || data.pay || ' ' || data.position);
end loop;
```

## 🪓프로시저(procedure)
> PL/SQL block을 데이터베이스에 저장하여 특정 작업을 수행하는, 이름이 있는 PL/SQL block 이다.
- 매개 변수를 받을 수 있고, 반복적으로 사용 할 수 있다.
- 보통 연속 실행 또는 구현이 복잡한 트랜잭션을 수행한다.


### 프로시저 생성
```sql
create procedure 프로시져이름 [{ 
	변수이름 데이터타입; --프로시져내에서 사용할 변수선언 변수이름 데이터타입; .... 
)] is 
begin
	기능 구현; 
end;
```
```sql
create or replace procedure p_test(name in varchar2) is
    begin 
        dbms_output.put_line(name || '님 합격 축하드립니다.');
    end;
exec p_test('양승완');
```
```sql
create or replace procedure p_userlist(
    id in userlist.id%type := 'kingsmile',
    name in userlist.name%type default '승완',
    age in userlist.age%type :=10,
    addr in userlist.addr%type := null
) is
begin
    insert into userlist values(id, name, age, addr);
    dbms_output.put_line('insert 정보 : ' || id || ' ' || name || ' ' || age || ' '  || addr);
end;
```

### 프로시저 실행

 ```sql
exec 프리시저이름(..매개변수);
```
```sql
exec p_userlist('seungwan', '양승완', 28, '경기도'); -- 전체 필드 추가
exec p_userlist;
```
- **=>** : 매개변수를 지정하여 데이터를 넣는다.
```sql
exec p_userlist(name=>'오연서', age=>33); -- 원하는 필드값만 추가 가능
```

### 저장된 프로시저 찾기
```sql
select * from user_objects
where lower(object_type)='procedure';
```

### 프로시저 작성된 쿼리문 찾기
```sql
select text from user_source
where lower(name) = '프로시저이름';
```

## ✂cursor
> 특정 SQL 문장을 처리한 결과를 담고 있는 영역 을 가리키는 일종의 포인터이다.
- 처리된 SQL 문장의 결과 집합에 접근할 수 있다.


### 커서 선언
```sql
cursor 커서이름 is select문장;
```
```sql
cursor c_emptype is select * from emp2 where emp_type = '정규직';
```

### 커서 열기
```sql
open 커서이름;
```
```sql
open c_emptype;
```
	
### 데이터 읽기
- loop 반복문을 활용한다.
```
fetch 커서이름 into 저장할 변수;
```
```sql
loop 
    fetch c_emptype into data;
    exit when c_emptype%notfound;
    dbms_output.put_line(data.name || ' ' || data.pay || ' ' || data.position);
end loop;
```

### 커서 닫기
```sql
close 커서이름;
```
```sql
close c_emptype;
```

### 커서 속성
- **%found** : 마지막으로 얻은 커서의 결과, 레코드가 있으면 참
- **%notfound** : 마지막으로 얻은 커서의 결과, 레코드가 없으면 참
- **%rowcount** : 커서에서 얻는 레코드 수 반환
- **isopen** : 커서가 열려있는지 확인, 열려 있으면 참

```sql
loop 
    fetch c_emptype into data;
    exit when c_emptype%notfound;
    dbms_output.put_line(data.name || ' ' || data.pay || ' ' || data.position);
end loop;
```

### 예제	
```sql
-- 문제3] emp2에서 날짜순으로 내림차순으로 정렬된 상태에서 5명을 출력하는 저장 프로시저
create or replace procedure p_birthday
is
    data emp2%rowtype;
    cursor c_birthday is select * 
			from (select * from emp2 order by birthday desc) 
			where rownum < = 5;
begin
    open c_birthday;
    loop
        fetch c_birthday into data;
        exit when c_birthday%notfound;
        dbms_output.put_line(data.birthday || ' ' || data.name || ' ' || 
					        data.emp_type || ' ' || data.pay || ' ' || data.position);
    end loop;
    dbms_output.put_line('결과 레코드수 => ' || c_birthday%rowcount);
    close c_birthday;
end;

exec p_birthday;
```
