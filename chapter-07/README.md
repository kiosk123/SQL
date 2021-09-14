# 테이블 조합

1. UNION  

하나의 엔티티를 성능상 이유로 여러 조건에 따라 나누기도 하는데 (매출정보등...) 이러한 흩어진 정보를  
합치는데 사용한다.  
  
UNION을 사용하면 합쳤을때 중복되는 로우는 삭제되며, UNION을 이용하여 합칠때 **최소한 컬럼의 갯수와 컬럼의 타입이 일치** 해야 한다.  
  
**UNION ALL**을 사용하면 중복된 로우도 포함 되어 데이터가 합쳐진다.

```SQL
SELECT * FROM TITEM WHERE CATEGORY = '식품'
UNION
SELECT * FROM TITEM WHERE CATEGORY = '가전';

-- UNION ALL 사용
SELECT DISTINCT DEPART FROM TSTAFF WHERE SALARY > 400
UNION ALL
SELECT DISTINCT DEPART FROM TSTAFF WHERE SCORE > 80;
```

2. INTERSECT  

두 테이블의 교집합을 구한다. 중복 레코드는 한번만 포함한다.  
UNION과 마찬가지로 교집합을 구할 때 **최소한 컬럼의 갯수와 컬럼의 타입이 일치** 해야 한다.

```SQL
SELECT NAME FROM TSTAFF
INTERSECT
SELECT MEMBER FROM TMEMBER;
```

3. MINUS
두 테이블의 차집합을 구한다.  
UNION과 마찬가지로 차집합을 구할 때 **최소한 컬럼의 갯수와 컬럼의 타입이 일치** 해야 한다.  
```SQL
-- 영업부 남성 직원만 구하기
SELECT NAME FROM TSTAFF WHERE DEPART = '영업부'
MINUS
SELECT NAME FROM TSTAFF WHERE GENDER = '여';

-- 영업부 직원이 아닌 여성 직원만 구하기
SELECT NAME FROM TSTAFF WHERE DEPART = '여'
MINUS
SELECT NAME FROM TSTAFF WHERE GENDER = '영업부';
```