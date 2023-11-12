
# SQL 커서란?
```
커서란 특정 SQL 문장을 처리한 결과를 담고있는 메모리 영역을 가리키는 일종의 포인터입니다. 
대부분의 SQL문 결과 ROW는 여러개인데 커서를 사용하면 이 ROW에 순차적으로 접근이 가능합니다.
```
- 커서의 종류에는 묵시적 커서와 명시적 커서가 있다. 

## 🤫 묵시적 커서(Implicit Cursor)
- 묵시적 커서는 오라클 내부에서 자동으로 생성되어 SQL문장이 실행될때마다 자동으로 만들어져 실행된다.
- 묵시적 커서는 자동적으로 선언해주는 SQL 커서로서, 사용자는 생성 유무를 알 수 없습니다.
- PL/SQL 블록 내에 있는 모든 SQL 문은 실제로는 암묵적 커서이다.  
- 명시적으로 선언하지 않아도 커서의 OPEN, FETCH, CLOSE를 자동 수행된다.

```
1. 쿼리가 길어지면 가독성 떨어짐
2. 자동관리가 편해짐
3. 재사용 불가능
```

### 묵시적 커서 속성(Cursor Attribute)
```
- SQL%ROWCOUNT: 로우 수 반환, 없으면 0  
- SQL%FOUND: 로우 수가 1개 이상이면 TRUE, 아니면 FALSE 
- SQL%NOTFOUND: 로우 수가 0이면 TRUE, 아니면 FALSE  
- SQL%ISOPEN: 묵시적 커서는 항상 FALSE
```

```sql
-- 10번 부서에 해당하는 모든 사원의 이름과 사원번호를 출력
BEGIN
    FOR D IN (
        SELECT ENAME, EMPNO
          FROM EMP
         WHERE DEPTNO = '10'
    ) LOOP
        DBMS_OUTPUT.PUT_LINE(D.ENAME || ' ' || D.EMPNO);
    END LOOP;
END;
```


## ✏️ 명시적 커서(Explicit Cursor)
 - 명시적 커서는 사용자가 직접 정의해서 사용하는 커서를 말합니다.
-  명시적 커서는 선언부 블록 내부에 커서를 정의할 때 생성된다.
-   명시적 커서는 사용자가 선언하여 생성 후 사용하는 SQL커서로, 주로 여러 개의 행을 처리하고자 할 경우 사용
-   사용자가 커서를 직접 OPEN, FETCH, CLOSE 해야 함
-   cursor FOR loop 문은 아래와 같이 암묵적으로 커서를 open, fetch, close 함

```
1. 가독성 좋아짐
2. 커서한번 선언시 반복 사용 가능 오픈
3. 클로즈 등 자동 관리가 부족함 -> 핸들링을 잘해야한다.
4. 비지니스 로직을 많이 처리하는 데에 좋다. 
```

### 명시적 커서 속성(Cursor Attribute)
   
- 커서이름%ROWCOUNT – 가장 마지막 행의 몇번째인지 반환 
- 커서이름%FOUND – 행이 있을 경우 TRUE, 아니면 FALSE
- 커서이름%NOTFOUND – 행이 없을 경우 TRUE, 아니면 FALSE
- 커서이름%ISOPEN – 메모리에 선언되어있을 경우 TRUE, 아니면 FALSE

### 명시적 커서 선언(Declaration)
```
CURSOR 커서명 IS 커서에 담고 싶은 데이터 서브쿼리
```

### 명시적 커서 열기(Open)
커서 선언 시 기술했던 서브쿼리를 수행해서 데이터를 커서로 가져 오는 과정
```
OPEN 커서 이름;
```

### 명시적 커서로부터 데이터 읽어서 변수로 할당하기(Fetch) 
```
FETCH 커서_이름 INTO 변수들;
```

### 명시적 커서 닫기(Close) 
```
CLOSE 커서_이름;
```

### 예제
```sql
-- - cursor FOR loop 문은 아래와 같이 암묵적으로 커서를 open, fetch, close 함
DECLARE
  CURSOR emp_cursor IS
    SELECT ename, hiredate
      FROM emp
     WHERE deptno = 10 
     ORDER BY hiredate desc;
BEGIN

    FOR i in emp_cursor LOOP
        DBMS_OUTPUT.PUT_LINE('건수: '||emp_cursor%ROWCOUNT||'입사일자: '|| i.hiredate );
    END LOOP;

END ;
```

```sql
DECLARE
	CURSOR emp_cur (p_dno emp.deptno%type) IS 
		   SELECT EMPNO, ENAME, SAL
		     FROM EMP
		    WHERE DEPTNO = p_dno;

BEGIN
	OPEN emp_cur(10) ;
	....
	OPEN emp_cur(20) ;
	....
	CLOSE emp_cur ;
	OPEN emp_cur(30) ;
	CLOSE emp_cur ;
END;
```


```sql
DECLARE
  -- 명시적 커서
  CURSOR emp_cursor IS
	     SELECT empno         사번
	          , ename         이름
	          , sal+NVL(comm,0) 급여 -- 
	       FROM emp;
	       
  v_emp_rec emp_cursor%ROWTYPE ; -- 데이터를 담기 위한 변수 선언

BEGIN
  OPEN emp_cursor ;
  
  DBMS_OUTPUT.PUT_LINE('사번  이름        총급') ;
  DBMS_OUTPUT.PUT_LINE('==== ========== ======') ;
  
  LOOP
    FETCH emp_cursor INTO v_emp_rec ;
    EXIT WHEN emp_cursor%NOTFOUND;
    DBMS_OUTPUT.PUT_LINE(v_emp_rec.사번||' '||v_emp_rec.이름||' '||
                         v_emp_rec.급여) ;
  END LOOP;
  
  CLOSE emp_cursor ;
END ;
```

