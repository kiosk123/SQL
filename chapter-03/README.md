# SELECT 문

## 1. 쿼리 연습에 사용할 데이터
쿼리 연습에 데이터를 사용하기 위해서 아래의 명령어를 실행하여 데이터를 디비에 집어넣는다
```sql
CREATE TABLE tCity
(
	name CHAR(10) PRIMARY KEY,
	area INT NULL ,
	popu INT NULL ,
	metro CHAR(1) NOT NULL,
	region CHAR(6) NOT NULL
);

INSERT INTO tCity VALUES ('서울',605,974,'y','경기');
INSERT INTO tCity VALUES ('부산',765,342,'y','경상');
INSERT INTO tCity VALUES ('오산',42,21,'n','경기');
INSERT INTO tCity VALUES ('청주',940,83,'n','충청');
INSERT INTO tCity VALUES ('전주',205,65,'n','전라');
INSERT INTO tCity VALUES ('순천',910,27,'n','전라');
INSERT INTO tCity VALUES ('춘천',1116,27,'n','강원');
INSERT INTO tCity VALUES ('홍천',1819,7,'n','강원');

CREATE TABLE tStaff
(
	name CHAR (15) PRIMARY KEY,
	depart CHAR (10) NOT NULL,
	gender CHAR(3) NOT NULL,
	joindate CHAR(10) NOT NULL,
	grade CHAR(10) NOT NULL,
	salary INT NOT NULL,
	score DECIMAL(5,2) NULL
);

INSERT INTO tStaff VALUES ('김유신','총무부','남','2000-2-3','이사',420,88.8);
INSERT INTO tStaff VALUES ('유관순','영업부','여','2009-3-1','과장',380,NULL);
INSERT INTO tStaff VALUES ('안중근','인사과','남','2012-5-5','대리',256,76.5);
INSERT INTO tStaff VALUES ('윤봉길','영업부','남','2015-8-15','과장',350,71.25);
INSERT INTO tStaff VALUES ('강감찬','영업부','남','2018-10-9','사원',320,56.0);
INSERT INTO tStaff VALUES ('정몽주','총무부','남','2010-9-16','대리',370,89.5);
INSERT INTO tStaff VALUES ('허난설헌','인사과','여','2020-1-5','사원',285,44.5);
INSERT INTO tStaff VALUES ('신사임당','영업부','여','2013-6-19','부장',400,92.0);
INSERT INTO tStaff VALUES ('성삼문','영업부','남','2014-6-8','대리',285,87.75);
INSERT INTO tStaff VALUES ('논개','인사과','여','2010-9-16','대리',340,46.2);
INSERT INTO tStaff VALUES ('황진이','인사과','여','2012-5-5','사원',275,52.5);
INSERT INTO tStaff VALUES ('이율곡','총무부','남','2016-3-8','과장',385,65.4);
INSERT INTO tStaff VALUES ('이사부','총무부','남','2000-2-3','대리',375,50);
INSERT INTO tStaff VALUES ('안창호','영업부','남','2015-8-15','사원',370,74.2);
INSERT INTO tStaff VALUES ('을지문덕','영업부','남','2019-6-29','사원',330,NULL);
INSERT INTO tStaff VALUES ('정약용','총무부','남','2020-3-14','과장',380,69.8);
INSERT INTO tStaff VALUES ('홍길동','인사과','남','2019-8-8','차장',380,77.7);
INSERT INTO tStaff VALUES ('대조영','총무부','남','2020-7-7','차장',290,49.9);
INSERT INTO tStaff VALUES ('장보고','인사과','남','2005-4-1','부장',440,58.3);
INSERT INTO tStaff VALUES ('선덕여왕','인사과','여','2017-8-3','사원',315,45.1);
```

# 2. SELECT 문 기본 형식
```sql
SELECT 필드목록
  FROM 테이블
[WHERE 조건]
[ORDER BY 정렬기준]
```

# 3. SELECT 문으로 조회시 특정 문자를 이스케이프
```sql
SELECT * FROM PRODUCT WHERE SALE LIKE '%30#%' -- 앞의 %는 와일드 카드 #뒤의 %는 백문율기호로 인식 즉 SALE 컬럼의 30%인 값을 찾는다
```

# 4-1. 특정 로우 번호에 해당하는 데이터만 가져오기 - 오라클
```sql
-- ROW_NUMBER 함수를 활용한 방식
SELECT RN, NAME
FROM (
    SELECT  ROW_NUMBER() OVER(ORDER BY AREA DESC) AS RN
          , NAME
          , AREA
          , POPU
          , METRO
          , REGION
    FROM TCITY
    ORDER BY AREA DESC
) A
WHERE RN <= 4
;

-- 서브쿼리에서 ORDER BY로 정렬 후 ROWNUM 의사 컬럼을 이용한 방식
SELECT ROWNUM, A.*
FROM
(
    SELECT  NAME
        , AREA
        , POPU
        , METRO
        , REGION
    FROM TCITY
    ORDER BY AREA DESC
) A
WHERE ROWNUM <= 4
```

# 4-2. 특정 로우 번호에 해당하는 데이터만 가져오기 - 마리아DB
기본 구조
```sql
SELECT ... LIMIT [건너뛸 개수], 총개수
```
총 개수를 생략하면 0으로 적용하여 첫 행부터 출력한다.
```sql
-- 면적이 넓은 상위 4개 도시 구한다.
SELECT * FROM tCity ORDER BY area DESC LIMIT 4;

-- 앞쪽 2개의 레코드는 건너뛰고 3개의 행을 구한다.
SELECT * FROM tCity ORDER BY area DESC LIMIT 2, 3;
```

# 4-3. 특정 로우 번호에 해당하는 데이터만 가져오기 DBMS공통
SQL 표준이 새로 만든 OFFSET FETCH 문법으로 DBMS마다 해당로우에 대한 데이터를 조회하는 방식을  
통일할 수 있다.  일부분을 특정하려면 순서가 지정되어야 하므로 **ORDER BY 문이 반드시 존재해야 한다.**  
OFFSET FETCH는 별도의 구문이 아니라 ORDER BY의 옵션이다
```sql
ORDER BY 기준필드 OFFSET 건너뛸행수 ROWS FETCH NEXT 출력할행수 ROWS ONLY
```

예제
```sql
-- 면적이 넓은 상위 4개 도시 구한다.
SELECT * FROM tCity ORDER BY area DESC OFFSET 0 ROWS FETCH NEXT 4 ROWS ONLY;
-- 앞쪽 2개의 레코드는 건너뛰고 3개의 행을 구한다.
SELECT * FROM tCity ORDER BY area DESC OFFSET 2 ROWS FETCH NEXT 3 ROWS ONLY;
-- 광역시를 제외하고 순위를 매겨 2등 부터 5등까지 출력
SELECT * FROM tCity WHERE metro = 'n' ORDER BY area DESC OFFSET 2 ROWS FETCH NEXT 3 ROWS ONLY;
```

# CREATE SELECT 문
`INSERT INTO 대상테이블(필드목록) SELECT 필드목록 FROM 원본테이블`와 비슷하지만 새로 테이블을 만들어 결과셋을 삽입한다.  
단 결과셋은 기존 테이블의 사본 데이터 이며 기본키나 외래키 같은 제약 조건까지 복사하지는 않는다.  
  
사본 테이블은 학습용이나 예비 처리의 타당성 점검등 자유롭게 사용하고 지우거나 백업 떠 놓는 용도로 쓰면된다.  
```SQL
CREATE TABLE 대상 테이블 AS SELECT 필드목록 FROM 원본테이블

CREATE TABLE TSTAFF_8월20일 AS SELECT FROM TSTAFF
``` 