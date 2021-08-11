# rownum
> 검색할 때 자동으로 행 번호를 추가해준다. 
- select만 가능
- rownum은 order by 전에 부여되며, order by는 맨 나중에 실행된다.

```sql
select rownum, ename, sal from emp;
```

```sql
select rownum, ename, sal from (select * from emp order by sal); 
-- 인라인 뷰를 사용하지 않고 사용하면, rownum이 뒤죽박죽 된다.
``