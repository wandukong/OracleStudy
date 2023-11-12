# Sub Program( Procedure, Function, Package)

## 🚥 프로시저(Procedure)
```
프로시저는 지정된 특정 처리를 실행하는 서브 프로그램의 한 유형.
```
- 프로시저는 결과값을 1개 이상 반환 가능
- 프로시져는 right opernads(우측 피연산자) 가 될 수 없으며, SQL문 내에서 사용할 수 없음
- SQL*Plus 내에서 프로시저를 EXECUTE하기 위해서 CALL문 또는 EXECUTE 문을 사용
- 형식 파라미터와 함께 또는 없이 프로시저를 정의할 수 있음.
- 파라미터 종류
	- IN
	- OUT
		- 초기화 되지 않은 변수
		- 기본값 할당 불가
	- IN OUT: IN OUT 매개변수를 사용하여 입력 변수로 사용하고, 이후 출력 변수로 사용
		- 초기화된 변수
		- 기본값 할당 불가

```sql
CREATE OR REPLACE PROCEDURE name 
	IN argument 
	OUT argument
    IN OUT argument 
IS
	[변수의 선언]
BEGIN
	[PL/SQL Block] -- SQL문장, PL/SQL제어 문장
	[EXCEPTION] --> 선택. error가 발생할 때 수행하는 문장
END;
---------------------------------------------------------------
-- 사원의 급여를 인상하는 프로시저
CREATE OR REPLACE PROCEDURE RAISE_SALARY(A_EMPNO NUMBER, A_AMT NUMBER)
IS
BEGIN
  -- 급여를 인상한다.
  IF A_AMT IS NOT NULL 
  THEN
    UPDATE EMP
       SET SAL = SAL + A_AMT
     WHERE EMPNO = A_EMPNO ;
    COMMIT;
  END IF ;
END ;
/

-- 사번을 입력받아 사원의 이름과 급여를 출력하는 프로시저
CREATE OR REPLACE PROCEDURE GET_ENAME_SQL(
    V_EMPNO IN  EMP.EMPNO%TYPE,  
    V_ENAME OUT EMP.ENAME%TYPE,
    V_SAL   OUT EMP.SAL%TYPE

) IS   
BEGIN
    SELECT ENAME, SAL
      INTO V_ENAME, V_SAL
      FROM EMP
     WHERE EMPNO = V_EMPNO;
END;
/
```


### 프로시저 호출 방법 3가지
```sql
-- 1. 저장 프로시저 실행: PL/SQL문에서 사용
BEGIN
  RAISE_SALARY(7788, 100) ;
END ;
/

-- 2. CALL 사용
CALL RAISE_SALARY(7788, 100) ;
/

-- 3. EXECUTE or EXEC 사용
EXECUTE RAISE_SALARY(7788, 100);
-- 저장 프로시저 실행: EXEC SQL*PLUS 명령은 EXECUTE의 약어임
EXEC RAISE_SALARY(7788, 100);
```

## 🗳️ 함수(Function)

- 함수는 결과값 1개만 반환 가능
- 함수는 정해진 작업을 수행 한 후 결과를 돌려준다(RETURN)
- SQL문 또는 PL/SQL 프로그램에서 직접적으로 호출할 수 있기 때문에 편리
- 모든 Stored functions 은 하나의 값을 반환해야 함.
- 하나의 값을 반환하기 때문에 우측 피연산자(right operands) 로 사용 가능

```sql
CREATE [OR REPLACE] FUNCTION function_name
	[( parameter1[mode1 ]datatype1 , parameter2[mode2 ]datatype2 , … )]
RETURN datatype -- 반환 타입 지정
IS | AS 
 -- 변수 선언
BEGIN
	[PL/SQL Block] -- SQL문장, PL/SQL제어 문장
END;
/
-------------------------------------------------------------------
-- 저장 함수
CREATE OR REPLACE FUNCTION GET_WAGE(A_EMPNO NUMBER)
RETURN NUMBER
-- 사원의 급여와 커미션의 합을 반환하는 함수
IS
  V_WAGE NUMBER ;
BEGIN
  -- 사번이 A_EMPNO인 사원의 급여와 커미션의 합을 조회한다.
  SELECT SAL + NVL(COMM,0) COMM
    INTO V_WAGE
    FROM EMP
   WHERE EMPNO = A_EMPNO ;
  -- 급여를 반환한다.
  RETURN V_WAGE ;
END ;
/
```

## 🎁 패키지

- 패키지는 연관성이 높은 함수나 프로시저를 하나의 그룹으로 묶어두는 개념.
- 패키지 선언부(specification)와 패키지 몸체부(body)로 구성.
	- 선언부
		- 해당 패키지에 사용될 함수나 프로시저, 변수 등 선언
		- 선언부는 전역 변수를 선언하며, 프로그램을 어떻게 호출하고 커서를 어떻게 열어야 하는지를 정의하고 몸체는 명세에 나열된 프로시저와 함수를 실제로 구현
		- 로컬 변수는 BODY에서 정의되며 오직 해당 PACKAGE의 프로시저 또는 함수 안 에서만 보임
	- 몸체부(Body)
		- 선언부에서 선언된 함수나 프로시저등이 실제 구현되는 부분
		-  패키지 선언부에서 선언되지 않더라도 패키지 몸체부에서 사용될 수는 있지만 별로 권장사항은 아니니 가급적 선언부에서 선언한 후 몸체부에서 사용해야 함
		- 본적으로 참조되는 변수든 서브프로그램이든 참조하는 서브프로그램보다는 먼저 정의되어야 함
- EXECUTE하기 위해서는 해당 패키지에 대하여 EXECUTE 권한을 부여 받아야 함
- 패키지 함수는 패키지 변수와 달리 SQL에서도 참조 가능
	- SELECT pkg_emp.get_wage(empno) FROM emp WHERE empno = 7788;
- 패키지 커서는 다른 프로그램에서 사용 가능
	- FOR cur IN pkg_emp.c_emp_cur

### Package 명세
```sql
CREATE OR REPLACE PACKAGE pkg_emp
IS
  -- 공용 타입 선언
  TYPE emp_type IS TABLE OF emp%ROWTYPE ;

  -- 공용 상수 선언
  c_deptno_accounting CONSTANT NUMBER := 10 ;
  c_deptno_research   CONSTANT NUMBER := 20 ;
  c_deptno_sales      CONSTANT NUMBER := 30 ;
  c_deptno_operations CONSTANT NUMBER := 40 ;
  
  -- 공용 변수 선언
  v_last_wage NUMBER ;
  
  -- 공용 서브프로그램 선언
  FUNCTION  get_wage(a_empno NUMBER) RETURN NUMBER ;
  PROCEDURE raise_bonus(a_empno NUMBER, a_amt NUMBER) ;
END ;
```

### Package Body
```sql
CREATE OR REPLACE PACKAGE BODY pkg_emp
IS
  --------------------------------------
  -- 전용 상수 선언
  --------------------------------------
  c_null_commission    CONSTANT NUMBER :=  0 ;
  c_failed_return_wage CONSTANT NUMBER := -1 ;

  --------------------------------------
  -- 전용 커서 선언
  --------------------------------------
  CURSOR emp_cursor(a_empno NUMBER) IS
    -- 사번이 a_empno인 사원의 급여와 커미션의 합을 조회한다.
    SELECT sal + NVL(comm, c_null_commission) comm
      FROM emp
     WHERE empno = a_empno ;
  
  --------------------------------------
  -- 공용 서브프로그램 정의
  --------------------------------------  
  FUNCTION get_wage(a_empno NUMBER) RETURN NUMBER
  -- 사원의 급여와 커미션의 합을 반환하는 함수
  IS
  BEGIN
    FOR rec IN emp_cursor(a_empno)
    LOOP
      -- 사원이 존재하는 경우에는 급여를 반환한다.
      v_last_wage := rec.comm ;
      return rec.comm ;
    end loop ;
    -- 사원이 존재하지 않을 경우는 -1을 반환한다.
    RETURN c_failed_return_wage ;
  END ;

  PROCEDURE raise_bonus(a_empno NUMBER, a_amt NUMBER)
  -- 테이블 bonus에 사원의 커미션 값을 인상하는 프로시저
  IS
    v_ename emp.ename%TYPE ;
  BEGIN
    -- 사원의 이름을 얻는다.
    BEGIN
      SELECT ename 
        INTO v_ename
        FROM emp
       WHERE empno = a_empno ;
    EXCEPTION
      WHEN NO_DATA_FOUND THEN
        -- 사원이 존재하지 않을 경우는 수행을 중단하고 복귀한다.
        -- 반환값이 없는 것이 함수와 다른 점이다.
        RETURN ;
    END ;
    
    -- 보너스를 인상한다.
    IF a_amt IS NOT NULL 
    THEN
      MERGE INTO bonus
      USING DUAL
         ON (bonus.ename = v_ename)
       WHEN MATCHED THEN -- 기존 보너스가 있는 경우는 인상할 값을 더한다.
         UPDATE SET comm = comm + a_amt
       WHEN NOT MATCHED THEN  -- 기존 보너스가 없는 경우는 새 로우를 추가
         INSERT (ename, comm) 
         VALUES (v_ename, a_amt) ;
    END IF ;
  END ;

BEGIN -- 패키지 초기화부
  v_last_wage := -1 ;
END ;
/

-- 패키지 변수는 다른 PL/SQL에서 참조 가능하다.
DECLARE
  v_var  NUMBER ;
BEGIN
  v_var := pkg_emp.get_wage(7788) ; -- pkg_emp.v_last_wage는 사번 7788의 sal이 된다.
  DBMS_OUTPUT.PUT_LINE('last wage = '||pkg_emp.v_last_wage) ;  -- 패키지 변수 참조
END ;
/

```

```sql
CREATE OR REPLACE PACKAGE emp_total 
AS
  PROCEDURE emp_sum;
  PROCEDURE emp_avg;
END;
/

CREATE OR REPLACE PACKAGE BODY emp_total  AS
    PROCEDURE emp_sum
    IS
      CURSOR emp_total_sum  IS
        SELECT COUNT(*), SUM(NVL(sal,0))
          FROM emp;
        total_num  NUMBER ;
        total_sum  NUMBER;
      BEGIN
        OPEN emp_total_sum ;
        FETCH emp_total_sum  INTO  total_num , total_sum ;
        DBMS_OUTPUT.PUT_LINE('총인원수: '||total_num||' , 급여합계: '||total_sum);
        CLOSE emp_total_sum;
      END emp_sum ; --emp_sum 프로시저 끝
    
   PROCEDURE emp_avg -- emp_avg 프로시저 시작
   IS
     CURSOR emp_total_avg  IS
      SELECT  COUNT(*), AVG(NVL(sal,0))
        FROM emp;
      total_num NUMBER ;
      total_avg  NUMBER ;
    BEGIN
      OPEN emp_total_avg ;
      FETCH emp_total_avg  INTO  total_num , total_avg;
      DBMS_OUTPUT.PUT_LINE('총인원수: '||total_num||' , 급여평균: '||total_avg);
      CLOSE emp_total_avg ;
    END emp_avg; -- 프로시저 끝
END emp_total; -- 패키지 끝
/

EXEC emp_total.emp_sum;
EXEC emp_total.emp_avg;
```


## 🌏 공통

### 중복 선언 가능
```sql
-- 저장되지 않는 서브 프로그램도 중첩과 중복 정의가 가능하다.
DECLARE
  -- 저장되지 않는 프로시저 정의
  PROCEDURE PRINT_ENAME(A_EMPNO NUMBER)
  IS
    -- PRINT_ENAME 내부에 중첩된 서브프로그램 정의
    FUNCTION GET_ENAME(A_EMPNO NUMBER) RETURN VARCHAR2
    IS
      V_ENAME DEPT.DNAME%TYPE ;
    BEGIN
      SELECT ENAME
        INTO V_ENAME
        FROM EMP
       WHERE EMPNO = A_EMPNO ;
      RETURN V_ENAME ;
    END ;
  BEGIN
    DBMS_OUTPUT.PUT_LINE(GET_ENAME(A_EMPNO)) ;
  END ;
  
  -- 3번 줄의 프로시저 PRINT_ENAME에 대한 중복 정의
  PROCEDURE PRINT_ENAME(A_ENAME VARCHAR2)
  IS
  BEGIN
    DBMS_OUTPUT.PUT_LINE(A_ENAME) ;
  END ;
  
BEGIN
  PRINT_ENAME(7566) ;    --  3번 줄의 PRINT_ENAME 호출
  PRINT_ENAME('SMITH') ; -- 21번 줄의 PRINT_ENAME 호출
END ;
/
```