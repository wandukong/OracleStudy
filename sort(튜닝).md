# SORT (튜닝)
- SORT 연산은 CPU의 부하를 높이고, 경우에 따라서는 사용자가 SELECT만 수행했는데 디스크 쓰기까지 발생시킬 수 있다.
- 그래서 SORT 연산을 피할 수 있는 방법과 부하를 줄일 수 있는 방법을 알야아 한다.

## 😜종류

**Order by(Sort Order by)**
- 실행계획에서 Sort Order by 연산으로 수행

**Group by(Sort Group by, Hash Group by)**
- Group by 명령과 Order by 명령을 SQL문에 동시에 사용할 때 **Sort Group by**로 수행
- Group by만을 수행할 때는 대부분 Hash Group by 연산으로 수행
- Group by의 결과값으로 **정렬된 데이터가 필요하다면 반드시 Order by 절 포함**해야 한다.
	- Order by를 생략하고 실행계획에서 Sort Group by 연산을 수행한다고 해서 정렬이 되는 것이 아니다.

**Union, Minus, Intersect (Sort Unique)**
- Union, Minus, Intersect를 사용할 때만 Sort Unique 연산이 수행

**Distinct (Sort Unique, Hash Unique)**
- SQL문에 따라 Sort Unique와 Hash Unique 연산을 사용
	- Order by를 사용했을 경우 Sort Unique 연산 수행

**In (Sort Unique)**
- In 절이 작성하기 편하지만, 비효율을 유발할 수 있다.
	- 정렬을 하기 때문에

**Rank(), Dense_Rank(), Row_Number() 등 윈도우 함수 (Window Sort)**
- 윈도우 함수를 사용하면 Window Sort 연산 수행

**Sort Aggregate 연산 (정렬X)**
- Sort로 시작하는 연산들 중에서 Sort Aggregate 연산은 정렬을 하는 것처럼 보이지만, 실제로는 정렬을 하지 않는다.

## 😝Index를 이용한 소트 대체
**Sort Order by**
- NULL을 허용하게 설계되었다면, 이를 수정해야만 Sort Order by 대신 Index를 이용해 Sort 연산을 대체하게 된다.
	- 컬럼에 실제로 NULL이 존재하고, NULL 데이터를 결과 집합에 포함시켜야 한다면 잘못된 방법

**Sort Group by Nosort**
- Group by를 수행할 때, NULL을 허용한 컬럼에 대해서 Where 절에 IS NOT NULL 조건을 넣어주면 Sort Group by No sort 연산 수행
	- 정렬을 대체할 인덱스를 사용하였기 때문에, Nosort라고 표시


## 🤪불필요한 소트 제거
**Union을 Union All로 수행**
- Union은 Sort Unique를 수행하기 때문에 정렬 발생
	- 만약, 중복값이 없다는 것을 인지하면 Union All로 대체한다.

**In절 안의 서브 쿼리를 Exists로 교체하고, 기존 인덱스 사용하거나 해당 컬럼으로 인덱스를 추가**
- Union은 Sort Unique를 수행하기 때문에 정렬 발생
```sql
SELECT /*+ LEADING(D) */
       *
FROM DEPT D
WHERE EXISTS (SELECT /*+ UNNEST */ 'X' FROM EMP WHERE DEPTNO = D.DEPTNO)

-- EXISTS
-- 서브쿼리의 결과가 한 건이라도 존재하면, TRUE
-- 존재하지 않으면, FALSE

-- UNNEST 힌트 (중첩된 서브쿼리(where절)에서 사용)
-- FILTER 동작방식을 선택하지 않고 조인 동작방식으로 처리하고자 할 때. 서브쿼리에 UNNEST 힌트 사용  

-- MERGE 힌트 (인라인 뷰(from절)에서 사용)
-- FROM 절에 나열된 테이블과 인라인 뷰를 합치는 시도를 한다.
SELECT I.ITEM_ID
       , I.ITEM_NM
       , (SELECT UNI_CD_NM FROM COM_CD WHERE UNI_CD = I.TEM_TYPE_CD)  ITEM_TYPE_NM /* 스칼라 서브 쿼리 */
       , A.SALE_PRICE
FROM ITEM I
     , (  /* 인라인 뷰 */
		  SELECT ITEM_ID
		         , SUM(SALE_PRICE) SALE_PRCIE
		  FROM UITEM_PRICE
		  GROUP BY ITEM_ID
     ) A
WHERE I.ITEM_ID = A.ITEM_ID
	  AND EXISTS ( /* 중첩된 서브 쿼리 */
	               SELECT 1
	               FROM ORD_ITEM
	               WHERE ITEM_ID = I.ITEM_ID
	                     AND ORD_DT BETWEEN '20120101' AND '20120131'
	  )
;
```

## 😋 피할 수 없는 소트, 소트 영역이라도 적게 사용하기
- 소트 영역은 로우의 개수와 더불어 컬럼들의 최종 길이도 함께 결정
	- 가로길이(컬럼 수)와 세로길이(로우 수)가 함께 결정
	- 필요한 컬럼만 정렬될 수 있도록, 그리고 필요한 만큼만 수행되도록 노력하기