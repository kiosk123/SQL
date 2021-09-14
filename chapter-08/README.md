# 테이블 병합

1. MERGE

조건에 따라서 테이블에 이미 조건에 맞는 데이터가 있으면 UPDATE나 DELETE를 처리할 수 있고
없으면 INSERT 처리를 한다.
```SQL
MERGE INTO 타겟 
    USING 소스 ON (일치조건)
WHEN MATCHED THEN
    UPDATE...
    DELETE...
WHEN NO MATCHED THEN
    INSERT ...
```

다음은 TCITYNEW 테이블을 TCITY에 병합하는 쿼리이다.
```SQL
-- TCITYNEW와 TCITY의 NAME 컬럼값이 일치하는 게 있으면 AREA와 POPU만 업데이트하고
-- 없으면 INSERT 한다
MERGE INTO TCITY T 
    USING TCITYNEW S ON (S.NAME = T.NAME)
WHEN MATCHED THEN
    UPDATE SET T.AREA = S.AREA, T.POPU = S.POPU
WHEN NO MATCHED THEN 
    INSERT VALUES (S.NAME, S.AREA, S.POPU, S.METRO, S.REGION);

-- 그리고 ON절의 일치 조건은 두 테이블의 대응 레코드를 찾는 것이지 병합할 대상 조건을
-- 지정하는 게 아니기 때문에 다음과 같이 처리하면 안된다.
-- 다음예제는 TCITYNEW의 REGION을 '경기'로 대상조건으로 지정하고 있으므로 잘못된 사용방법이다.
MERGE INTO TCITY T USING TCITYNEW S ON (S.NAME = T.NAME AND S.REGION = '경기')
...
```

다른 테이블에서 직접 데이터를 가져오는 게 아니라, 직접 값을 넣고자 한다면  
DUAL을 사용하여 차리할 수 있다.
```SQL
MERGE INTO TB_SCORE S
    USING DUAL ON (S.COURSE_ID = 'C1' AND S.STUDENT_ID = 'S1')
WHEN MATCHED THEN
    UPDATE SET S.SCORE = 20
WHEN NOT MATCHED THEN
    INSERT (S.COURSE_ID, S.STUDENT_ID, S.SCORE)
    VALUES ('C1', 'S1', 20);
```

업데이트나 INSERT만 할 수도 있다.  
존재하면 아무것도 하지 않고, 없을때 INSERT 하려면 다음과 같이 한다.
```SQL
MERGE INTO TB_SCORE S
    USING DUAL ON (S.COURSE_ID = 'C1' AND S.STUDENT_ID = 'S1')
WHEN NOT MATCHED THEN
    INSERT (S.COURSE_ID, S.STUDENT_ID, S.SCORE)
    VALUES ('C1', 'S1', 20);
```