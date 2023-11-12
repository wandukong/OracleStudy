

# Exception

## ⚠️ 예외란?
- PL/SQL 블록이 실행되는 동안에 발생되는 에러
- 런타임 에러를 예외(Exception)라고 칭함.
	- PL/SQL 블록의 선언부, EXECUTE부, 예외 처리부 내에서 발생할 수 있음.
- 두 개의 내장 함수 : SQLCODE 와 SQLERRM를 통해 에러 관련 정보를 얻을 수 있다.

```sql
DECLARE
  v_ename emp.ename%TYPE NOT NULL := ' ';
  v_mgr   emp.mgr  %TYPE NOT NULL := -1;
  v_code  NUMBER;
  v_errm  VARCHAR2(64);
BEGIN
   SELECT ename, mgr INTO v_ename, v_mgr
     FROM emp
    WHERE ename = 'KING';
EXCEPTION  -- 예외 처리
     WHEN NO_DATA_FOUND THEN -- SELECT되는 건이 없음
          DBMS_OUTPUT.PUT_LINE('조회되는 값이 없습니다.') ;
     WHEN TOO_MANY_ROWS THEN -- SELECT되는 건이 한 건보다 많음
	      DBMS_OUTPUT.PUT_LINE('조회되는 건수가 한 건보다 많습니다.') ;
	 WHEN OTHERS THEN -- 어떠한 오류가 발생하더라도 처리한다.
	      DBMS_OUTPUT.PUT_LINE('error: ' || sqlerrm(sqlcode));
```


## 🧑🏻‍💻 사용자 정의 예외

### 
- 선언부 내에 EXCEPTION 변수를 선언
- 변수를 선언하고, 그 이름으로 예외 처리 구현
- 변수를 선언하고, 유효한 에러 코드에 맵핑 하도록 구현


### PRAGMA EXCEPTION_INIT

- PRAGMA 선언 : 특정 에러코드를 예외 메시지로 바꿔주는 역할
- EXCEPTION_INIT: 사용하여 exception 을 에러 코드와 매핑
```sql
DECLARE
    v_empno EMP.ENAME%TYPE;
    v_cnt PLS_INTEGER;
    
    nodata EXCEPTION;
    PRAGMA EXCEPTION_INIT(nodata, 100); 

BEGIN 
    v_empno:= -1;
    
    SELECT empno INTO v_cnt FROM EMP WHERE empno  = v_empno;
    
    update emp set sal = sal*1.05 where empno = v_empno;
    EXCEPTION WHEN nodata THEN
        DBMS_OUTPUT.PUT_LINE('no data found!!!');
END;
```

### RAISE
- 예외 처리를 위한 변수를 선언한 후 특정 조건에서 예외가 발생하도록 RAISE
- 사용 후 EXCEPTION 절에서 예외 처리 로직을 기술
```sql
DECLARE
   e EXCEPTION;
BEGIN
   RAISE e;
   dbms_output.put_line('Can''t get here.');
   
   EXCEPTION 
        WHEN OTHERS THEN
             IF SQLCODE = 1 THEN --사용자 정의 exception은 SQLCODE default 값이 1
                dbms_output.put_line('This is a ['||SQLERRM||'].');
             END IF;
END;
```

### RAISE_APPLICATION_ERROR
```sql
-- RAISE_APPLICATION_ERROR를 사용하여 발생시킨 예외를 OTHERS로 처리
-- 예외가 발생하지 않음
DECLARE
  v_deptno dept.deptno%TYPE := 30 ;
  v_empno  emp.empno  %TYPE := 9900 ;
  v_dname  dept.dname %TYPE ;
  v_ename  emp.ename  %TYPE ;
BEGIN
  SELECT MAX(dname) dname
    INTO v_dname
    FROM dept
   WHERE deptno = v_deptno ;
    
  IF v_dname IS NULL THEN
    RAISE_APPLICATION_ERROR(-20001, '부서 번호 '||v_deptno||'이 존재하지 않습니다.') ;
  END IF ;

  BEGIN
    SELECT ename
      INTO v_ename
      FROM emp
     WHERE empno = v_empno ;
    EXCEPTION WHEN NO_DATA_FOUND THEN
	    RAISE_APPLICATION_ERROR(-20002, '사원 번호 '||v_empno||'이 존재하지 않습니다.') ;
  END ;
  
  DBMS_OUTPUT.PUT_LINE('부서와 사원 데이터에 이상이 없습니다.') ;
  
EXCEPTION WHEN OTHERS THEN
  IF SQLCODE = -20001 THEN
    DBMS_OUTPUT.PUT_LINE('부서 오류') ;
    DBMS_OUTPUT.PUT_LINE(SQLERRM) ;
  ELSIF SQLCODE = -20002 THEN
    DBMS_OUTPUT.PUT_LINE('사원 오류') ;
    DBMS_OUTPUT.PUT_LINE(SQLERRM) ;
  END IF ;
END ;

```