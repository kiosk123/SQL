# 함수

많은 DB에서 사용하는 주요함수들 정리함

## 1. AVG(평균), MOD(나머지)  
평균을 구한다.

```SQL
SELECT AVG(SCORE) 
FROM TSTAFF
WHERE DEPART = '영업부';
```

나머지를 구한다.
```SQL
SELECT MOD(10, 3) FROM DUAL;
```

## 2. ROUND, TRUNC  
소수점 N자리까지만 남기고 N자리 다음 자리를 반올림하여 소수점 N자리를 표시한다.  
```SQL
-- ROUND(값 또는 컬럼, 표시할 소수점 N자리) - 예) 52.23
SELECT ROUND(AVG(SCORE),2) FROM TSTAFF WHERE DEPART = '영업부'
```
소수점 N자리까지만 남기고 N자리 다음 자리를 반올림하지 않고 소수점 N자리를 표시한다.(N+1까지 자리 버림)
```SQL
SELECT TRUNC(867.567, 1) FROM DUAL; -- 867.5
```

## 3. REPLACE, REGEXP_REPLACE  - 치환과, 정규식을 통한 치환
문자열 내의 특정 글자를 찾아 다른 글자로 치환
```SQL
-- 부를 팀이라는 글자로 치환
SELECT NAME, REPLACE(DEPART, '부', '팀') FROM TSTAFF
```
정규식 패턴을 통한 치환도 가능하다
```SQL
SELECT ENAME, REGEXP_REPLACE(SAL, '[0-3]', '*')
FROM EMP;
```

## 4. 문자열 연결  
```SQL
--오라클 || 를 이용
SELECT TRIM(NAME) || ' ' || GRADE, SALARY FROM TSATFF WHERE DEPART = '인사과'

--오라클 CONCAT 사용 -> 오라클 CONCAT은 인자 두개가 고정이기 때문에 사용하려면 다음과 같이 사용
SELECT CONCAT(CONCAT(REGION, '도의'), NAME) SALARY FROM TCITY

--SQL Server
SELECT TRIM(NAME) + ' ' + GRADE, SALARY FROM TSATFF WHERE DEPART = '인사과'

-- MariaDB - 오라클에서도 CONCAT 함수를 사용가능하지만 인자는 두개로 고정되어 있음
SELECT CONCAT(TRIM(NAME),'  ',GRADE) SALARY FROM TSATFF WHERE DEPART = '인사과'
```

## 5. 문자열 검색 
**오라클 - INSTR**  
문자열에서 특정 문자가 어디에 위치해 있는지 검색한다.  
```SQL
--INSTR(검색 대상 문자열, 검색 패턴, 시작위치, 순서) 시작위치와 순서생략시 모두 1 적용
--문자열을 찾지 못하면 0을 반환
SELECT INSTR('우리나라 대한민국', '나라') FROM DUAL; -- 3
SELECT INSTR('우리나라 대한민국', '민족') FROM DUAL; -- 0

SELECT INSTR('국민에 의한 국민을 위한 국민의 국민당', '국민', 3) FROM DUAL; -- 3번째 부터 국민 글자를 검색하므로 결과 값 8
SELECT INSTR('국민에 의한 국민을 위한 국민의 국민당', '국민', 1, 3) FROM DUAL; -- 첫번째 부터 3번째에 나타나는 국민 글자를 검색하므로 결과값 15
SELECT INSTR('국민에 의한 국민을 위한 국민의 국민당', '국민', -1) FROM DUAL; -- 시작위치를 음수로 주면 끝에서 부터 검색하여 결과 값 19
```
**SQL Server - CHARINDEX(패턴, 검색대상문자열, 시작위치) 순서지정 불가능**
```SQL
SELECT CHARINDEX('국민', '국민에 의한 국민을 위한 국민의 국민당', 3) FROM DUAL; --3
```

**MariaDB - POSITION은 시작 위치도 지정할 수도 없으며 제일 처음 찾은 패턴 위치를 리턴한다.**
```SQL
SELECT POSITION('국민' IN '국민에 의한 국민을 위한 국민의 국민당'); --1
```

## 6. 문자열 추출  
**SUBSTR(대상문자열, 시작위치, 추출길이)**  
```SQL
SELECT SUBSTR('아름다운 대한민국 금수강산', 6, 4) FROM DUAL; -- 대한민국
SELECT SUBSTR('아름다운 대한민국 금수강산', -4, 2) FROM DUAL; -- 금수
```

## 7. NULL 관련  
NULL값일 경우 디폴트 값으로 변환하는 방법
```SQL
--오라클
SELECT NAME, NVL(SCORE, 10) FROM TSTAFF -- SCORE가 NULL일 경우 디폴트값을 10으로 설정

--SQL Server
SELECT NAME, ISNULL(SCORE, 10) FROM TSTAFF

--MariaDB
SELECT NAME, IFNULL(SCORE, 10) FROM TSTAFF
```

**NULLIF** - 컬럼 값이 특정 값일 경우 NULL로 변환
```SQL
SELECT NAME, NULLIF(SCORE, 0) FROM STAFF -- SCORE 컬럼의 값이 0이면 NULL로 변환
```

**NVL2** - NULL인 경우와 아닌 경우에 따라서 값 설정
```SQL
SELECT NAME, NVL2(SCORE, SALARY * SCORE / 100, 50) FROM TSTAFF -- SCORE가 NULL이 아닐경우  SALARY * SCORE / 100으로 설정 NULL일경우 50으로 설정
```

**COALESCE** 함수는 복수 개의 인수를 가지며 NULL이 아닌 첫번째 값을  리턴한다.  
만약 전부 NULL일경우 NULL을 반환한다.
```SQL
-- 지망1 컬럼이 NULL이면 지망2를 출력, 지망2 컬럼이 NULL이면, 지망3를 출력 지망3 컬럼이 NULL이면 NULL 출력
SELECT COALESCE(지망1, 지망2, 지망3) FROM 입사지원서
```

**DECODE** - 오라클만 지원  
필드의 값을 다른 값으로 변환한다.  
DECODE(대상컬럼, 대상값1, 변환결과값1, 대상값2, 변환결과값2,.... 기본값)

```SQL
-- GENDER 컬럼의 값이 남이면 멋쟁이로 변환, 여이면 예쁜이로 변환 그외는 몬난이로 변환
SELECT NAME, DECODE(GENDER, '남', '멋쟁이', '여', '예쁜이', '몬난이') FROM TSTAFF;

-- GENDER 컬럼의 값이 남이면 멋쟁이로 변환 그외는 보통사람으로 변환
SELECT NAME, DECODE(GENDER, '남', '멋쟁이', '보통사람') FROM TSTAFF; 

```

## 8. 날짜와 시간
### 오라클
|함수|설명|
|------|------|
|SYSDATE|현재 날짜를 조사. 운영체제 기준, 초단위|
|CURRENT_DATE|접속 세션의 현재 날짜를 조사한다. 초단위|
|SYSTIMESTAMP|시간대를 기준으로 한 현재 시간을 구한다. 나노초 단위|
|TRUNC|시간 단위를 버림한다.|
|ROUND|정오를  기준으로 반올림한다|
|LAST_DAY(d)|지정한 날짜가 속한 월의 마지막 날을 구한다.|
|ADD_MONTH(date, n)|n개월을 더한다.|
|MONTHS_BETWEEN(d1, d2)|두 날짜의 개월수 차이를 구한다.|
|NEXT_DAY(d, c)|d 날짜의 다음 c요일을 찾는다. `NEXT_DAY('2022-03-31', '월요일')`|
|EXTRACT(what FROM d)|d날짜의 요소를 조사한다. YEAR, MONTH, DAY, HOUR, NINUTE, SECOND등의 요소를 지정한다.|

```SQL
SELECT SYSDATE + 12 FROM DUAL; -- 현재 시간에서 12일을 더한다
SELECT SYSDATE + 5/24 FROM DUAL; -- 현재 시간에 5시간을 더한다. 
SELECT SYSDATE - 30/1440 FROM DUAL; -- 현재 시간에 30분을 뺀다
SELECT SYSDATE - 80/86400 FROM DUAL; -- 현재 시간에 80초를 뺀다
```
### SQL Server의 날짜 함수
|함수|설명|
|------|------|
|GETDATE()|현재 날짜를 구한다|
|GETUTCDATE()|현재 날짜와 시간을 국제 표준시로 구한다. 우리나라보다 9시간 느리다|
|DATEPART(요소, 날짜)|날짜에서 요소를 분리한다.|
|DATENAME(요소, 날짜)|날짜에서 요소를 문자열로 분리한다.|
|YEAR(날짜)|날짜값에서 연도만 분리한다. YEAR(GETDATE())는 현재 년도만 출력한다.|
|MONTH(날짜)|날짜값에서 달만 분리한다.|
|DAY(날짜)|날짜값에서 날짜만 분리한다|
|DATEADD(요소,값,날짜)|날짜에서 특정 요소를 더한다.|
|DATEDIFF(요소, 날1, 날2)|두 날짜 사이의 차이를 요소 단위로 구한다|

### MariaDB의 날짜 함수
|함수|설명|
|------|------|
|CURDATE|현재 날짜를 조사한다.|
|CURTIME|현재 시간을 조사한다.|
|NOW, SYSDATE|현재 날짜와 시간을 조사한다.|
|DATE_FORMAT|날짜를 원하는 형식으로 포매팅한다.|
|STR_TO_DATE|문자열을 날짜 타입으로 변환한다.|
|UNIX_TIMESTAMP|1970.1.1 자정 이후 경과 초, 에폭타임|
|FROM_UNIXTIME|에폭타임을 날짜 포맷으로 변환|
|DATE_ADD|날짜 더하기|
|DATE_SUB|날짜 빼기|
|DATE_DIFF|날짜 차이 구하기|

```SQL
SELECT DATE_FORMAT(NOW(), '%Y%m%d %H:%i:%s'); -- 20220331 01:39:05
SELECT DATE_DIFF(NOW(), JOINDATE) FROM TSTAFF; -- 두날짜간의 사이를 구함
```