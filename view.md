
#View
> 가상 테이블
- 실제 존재하지 않지만 실제 테이블과 같이 사용한다.
     (select , insert , delete, update 가능)
- 복잡한 쿼리문(join, sub query)을 미리 뷰로 만들어 저장하면, 다음부터 저장한 view의 정보만 가져오면 되므로 쉽게 좀 더 편리하게 사용할 수 있다. (insert , delete, update 불가능)
- 보안을 위해 사용한다. (관리자 유형에 따라 특정 컬럼만 선택하여 보여준다)
- 원본 테이블을 제거하면, **뷰도 제거**된다.
- 원본 테이블에 데이터를 삽입·삭제·수정하면, **뷰에도 적용**된다.
- **집계 함수**에 **별칭**을 지정해줘야 한다.

## 🍬생성
```sql
create view 뷰이름 as 뷰내용;
```
```sql
create view v_emp30 as select job, ename, sal from emp where deptno=30;
```

## 🍭수정
```sql
create or replace view 뷰이름 as 뷰내용;
```
```sql
create or replace view v_emp as select empno, ename, deptno from emp;
```

## 🍡삭제
```sql
drop view 뷰이름;
```