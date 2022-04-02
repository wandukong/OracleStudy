# WITH AS
```sql
/* 한 개 사용*/
WITH 이름 AS (
	SELECT ...
	FROM ...
) 
```
```sql
/* 중복  사용*/
WITH 이름 AS (
	SELECT ...
	FROM ...
) 이름2 AS (
	SELECT ...
	FROM ...
)
```
```sql
WITH T_EMP AS (
	SELECT *
	FROM SAL > 10000
) T_EMP2 AS (
	SELECT *
	FROM SAL <= 10000
)

SELECT EMP_NM
FROM T_EMP;

SELECT AGE
FROM T_EMP2;
```
- 임시 테이블을 만드는 이름이 부여된 서브 쿼리
- 복잡한 SQL에서 동일 블록에 대해 반복적으로 SQL문을 사용하는 경우 그 블록에 이름을 부여하여 재사용 할 수 있게 함으로써, 쿼리 성능을 높일 수 있다.
- WITH절에 정의된 쿼리는 여러 번 사용할 수록 효율이 증가한다.
- VIEW와 비슷하지만, VIEW는 생성 후 DROP을 하지 않으면 없어지지 않는다