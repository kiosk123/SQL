# 임시테이블

임시테이블은 데이터를 잠시만 보유하는 테이블이다. 영구적인 정보가 아닌 일시적인 중간데이터가 필요할 때 사용한다.  
뷰와 비슷하지만 뷰는 정의만 가지는데 비해 임시 테이블은 비록 일시적이지만 진짜 데이터를 보유하는 테이블이다.  
  
복잡한 쿼리를 이용해서 데이터를 추출하는 대신에 임시테이블에 원본 데이터를 복사하여 넣고 필요한 데이터만 조건에 맞춰  
삭제하고 출력함으로써 원하는 복잡한 쿼리없이 원하는 데이터만 빠르게 추출할 수 있다.

### 1. 오라클 임시 테이블  

오라클은 데이터는 임시적이지만 테이블은 영구적이다
```sql
--임시테이블 생성
CREATE GLOBAL TEMPORARY TABLE 테이블명 (
    필드속성
) [ON COMMIT DELETE ROWS 또는 ON COMMIT PRESERVE ROWS]

--ex)
CREATE GLOBAL TEMPORARY TABLE TGTT(
    NAME VARCHAR(20) PRIMARY KEY,
    SCORE INT
);

-- 임시테이블 삭제
DROP TABLE 임시테이블명
```
임시테이블 생성 옵션 설명
- ON COMMIT DELETE ROWS : 생략시 디폴트 옵션 - 커밋하면 데이터가 사라진다
- ON COMMIT PRESERVE ROWS : 세션중에는 데이터를 유지하며 세션이 종료하면 사라진다

### 2. SQL Server 임시 테이블 
오라클과 반대로 테이블마저도 임시적이다.

```sql
--테이블명 앞에 #을 붙이면 임시테이블이 된다
CREATE TABLE #테이블명 ( 
    필드속성
)

-- 임시테이블에 CRUD시 테이블명에 #을 꼭 붙일 것
INSERT INTO #테이블명 VALUES('김한솔','95');
```
### 3. Maria DB 임시 테이블 
오라클과 비슷하게 TEMPORARY 키워드를 사용한다
```sql
--임시테이블 생성
CREATE GLOBAL TEMPORARY TABLE 테이블명 (
    필드속성
) 
```
