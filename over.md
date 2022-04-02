# OVER

```sql
분석함수 OVER(PARTITION BY 컬럼 | ORDER BY 컬럼 | 세부 분할 기준)
```
```sql
SELECT EMP_ID
	   ,SALARY AS "급여"
	   ,AVG(SALARY) OVER() AS "평균급여"
	   ,SUM(SALARY) OVER() AS "총급여"
FROM EMP
```
- 분석함수는 필수 작성
- OVER() 안의 내용들은 필요한 경우 작성
- GROUP BY 나 SUB QUERY를 사용하지 않고, count, sum, avg 등과 같은 분석함수를 사용할 수 있다.
- 집계 함수 사용시 서브쿼리가 지나치게 길어지는 것과 GROUP BY를 사용해야하 는 것을 줄일 수 있다.

## PARTITION BY 
- 어느 컬럼을 기준으로 분할할 지를 의미한다.
- GROUP BY 와 같은 역할을 한다.
```sql
/* 직무별평균급여를 구함 */
SELECT EMP_ID
	   ,SALARY AS "급여"
	   ,AVG(SALARY) OVER(PARTITION BY JOB) AS "직무별평균급여"
	   ,AVG(SALARY) OVER() AS "평균급여"
FROM EMP
```
## ORDER BY
- 어느 컬럼을 기준으로 정렬할 지를 의미한다.
```sql
/* 직무별평균급여를 구하는데 직무이름으로 정렬 */
SELECT EMP_ID
	   ,SALARY AS "급여"
	   ,AVG(SALARY) OVER(PARTITION BY JOB ORDER BY JOB) AS "직무별평균급여"
	   ,AVG(SALARY) OVER() AS "평균급여" 
FROM EMP
```
