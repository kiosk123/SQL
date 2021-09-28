# 뷰(VIEW)

1. 뷰 생성
```SQL
CREATE VIEW 이름(필드목록) AS
SELECT 문

-----------------------------------------------------------------------------------------------
-- 원본 테이블의 특정 컬럼으로만 뷰를 만듬 - 수직뷰
CREATE VIEW VMEMBER AS
SELECT MEMBER, AGE, ADDR FROM TMEMBER;

--원본 테이블의 특정 조건을 만족하는 특정 컬럼으로만 뷰를 만듬 - 수직수평뷰(조건만 들어갈 경우 수평뷰)
CREATE VIEW VSTAFFPART AS 
SELECT NAME, SALARY FROM TSTAFF WHERE DPART = '총무부';

--뷰를 만들때 필드이름을 지정
CREATE VIEW VMEMBER(M, A, AD) AS
SELECT MEMBER, AGE, ADDR FROM TMEMBER;

SELECT M, A, AD FROM VMEMBER; -- 뷰를 만들때 지정한 필드이름으로 필드 조회
```

2. 뷰 제거
```SQL
DROP VIEW VIEW 이름
-----------------------
DROP VIEW VMEMBER;
```

3. 뷰 변경
```SQL
--오라클, MariaDB
CREATE OR REPLACE VIEW VMEMBER AS
SELECT MEMBER, AGE, ADDR, EMAIL FROM TMEMBER;

--VSTAFFALIAS 컬럼명 변경 - 1
CREATE OR REPLACE VIEW VSTAFFALIAS (N, D, S) AS
SELECT NAME, DEPART, SALARY FROM TSTAFF

--VSTAFFALIAS 컬럼명 변경 - 2
CREATE OR REPLACE VIEW VSTAFFALIAS AS
SELECT NAME N, DEPART D, SALARY S FROM TSTAFF

------------------------------------------------------------

--SQL Server
ALTER VIEW VMEMBER AS 
SELECT MEMBER, AGE, ADDR, EMAIL FROM TMEMBER;
```

