# 서브쿼리

1. 단일행 서브쿼리  
서브쿼리의 결과가 하나뿐인 쿼리
  
최대 인구수를 가진 도시 구하기
```sql
SELECT NAME 
FROM TCITY 
WHERE POPU = (SELECT MAX(POPU) FROM TCITY);
```

2. 서브쿼리 중첩  
70000만원 짜리 상품을 구입한 회원나이 구하기
```SQL
SELECT AGE FROM TMEMBER WHERE MEMBER = (
    SELECT MEMBER FROM TORDER WHERE ITEM = (
        SELECT ITEM FROM TTIEM WHERE PRICE = 70000
    )
);
```

3. 다중행 서브쿼리  
서브쿼리의 결과 로우가 여러개면 다중행 서브쿼리라고 한다.  

향단이 구입한 상품의 이름과 가격
```SQL
SELECT ITEM, PRICE FROM TITEM WHERE ITEM IN
(SELECT ITEM FROM TORDER WHERE MEMBER = '향단');
```

4. 다중열 서브쿼리
다중열 서브쿼리는 결과셋의 컬럼이 하나밖에 없으며 그래서 특정값과 비교할 수 있다.  
이에 비해 다중열 서브쿼리는 결과셋의 컬럼이 여러 개이며 한꺼번에 여러값과 비교한다.  
  
오라클과 MariaDB는 지원하지만 SQL Server는 아직 지원하지 않는다.  
  
다음은 안중근과 성(gender)이 같으며 같은 부서에서 일하는  동료를 조사하는 다중쿼리다.
```sql
SELECT * FROM TSTAFF WHERE (DEPART, GENDER) =
(SELECT DEPART, GENDER FROM TSTAFF WHERE NAME = '안중근');
```

5. 다중열이면서 다중행인 서브쿼리  

다음 쿼리문은 부서별 최고 월급자의 목록을 조사한다.  
여러개의 부서가 있고 각 부서명과 최고 월급을 비교해야 한다.  

```sql
SELECT * FROM TSTAFF WHERE (DEPART, SALARY) IN 
(SELECT DEPART, MAX(SALARY) FROM TSTAFF GROUP BY DEPART);
```

6. 서브쿼리 연산자 ANY, ALL  
IN과 달리 ANY와 ALL은 모든 결과 셋과 비교히며 ANY는 그중 하나라도 만족하는 값이 있으면 참이고,  
ALL은 모두 만족해야 참이다.

```SQL
-- 영업부 직원모두 비교하여 영업부 직원보다 한명이라도 월급 더 많이 받는 직원 찾기
SELECT NAME FROM TSTAFF WHERE SALARY > ANY
(SELECT SALARY FROM TSTAFF WHERE DEPART = '영업부');

SELECT NAME FROM TSTAFF WHERE SALARY > 
(SELECT MIN(SALARY) FROM TSTAFF WHERE DEPART = '영업부');
-- 영업부 직원모두 비교하여 월급 많이 더 많이 받는 직원 찾기
SELECT NAME FROM TSTAFF WHERE SALARY > ALL
(SELECT SALARY FROM TSTAFF WHERE DEPART = '영업부');

SELECT NAME FROM TSTAFF WHERE SALARY > 
(SELECT MAX(SALARY) FROM TSTAFF WHERE DEPART = '영업부');
```

일반적으로 ANY와 ALL은 부등 연산자와 함께 사용한다. 등호도 가능하지만 하나의 값을 여러개의 값과  
상등 비교하는 것은 의미가 없다.  
```SQL
SELECT ITEM, PRICE FROM TITEM WHERE ITEM = ANY
(SELECT ITEM FROM TORDER WHERE MEMBER = '향단');
```