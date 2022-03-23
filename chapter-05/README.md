# 외래키 제약

직원과 프로젝트 관계는 참조 관계다
```SQL
CREATE TABLE TEMPLOYEE
(
	NAME CHAR(10) PRIMARY KEY,
	SALARY INT NOT NULL,
	ADDR VARCHAR(30) NOT NULL
);

INSERT INTO TEMPLOYEE VALUES ('김상형', 650, '이천시');
INSERT INTO TEMPLOYEE VALUES ('문종민', 480, '대구시');
INSERT INTO TEMPLOYEE VALUES ('권성직', 625, '안동시');

/* 프로젝트 테이블은 직원 테이블을 참조하고 있다.*/
CREATE TABLE TPROJECT
(
	PROJECTID INT PRIMARY KEY,
	EMPLOYEE CHAR(10) NOT NULL,
	PROJECT VARCHAR(30) NOT NULL,
	COST INT,
    /* 외래키 참조 설정  - 프로젝트의 EMPLOYEE 컬럼이 TEMPLOYEE 테이블의 NAME을 참조하고 있음*/
    CONSTRAINT FK_EMP FOREIGN KEY(EMPLOYEE) REFERENCES TEMPLOYEE(NAME) 
);

INSERT INTO TPROJECT VALUES (1, '김상형', '홍콩 수출건', 800);
INSERT INTO TPROJECT VALUES (2, '김상형', 'TV 광고건', 3400);
INSERT INTO TPROJECT VALUES (3, '김상형', '매출분석건', 200);
INSERT INTO TPROJECT VALUES (4, '문종민', '경영 혁신안 작성', 120);
INSERT INTO TPROJECT VALUES (5, '문종민', '대리점 계획', 85);
INSERT INTO TPROJECT VALUES (6, '권성직', '노조 협상건', 24);
INSERT INTO TPROJECT VALUES (7, '홍길동', '원자재 매입', 900);
```

위와 같이 외래키 설정을 하면 INSERT시 직원 -> 프로젝트 테이블 순으로 해야되고  
반대로 DELETE식 프로젝트 -> 직원 테이블 순으로 해야된다.  
  
외래키 제약이 설정되어 있는 상태에서 `DROP TABLE TEMPLOYEE`를 실행하면 에러가 발생한다.  
  
외래키 제약이 너무 강력해서 가끔 불편할 때가 있다. 직원을 삭제하려면 이 직원이 담당한 프로젝트를 먼저 삭제하는  
단계를 거쳐야 한다. 실수를 방지하기 위한 제약이지만 진짜 지워야 하는 경우가 빈번하다면 매번 순서대로 작업하기 번거롭다.  
  
그래서 **관련 작업을 자동화 하여 한 번에 처리하는 연계 참조 무결성 제약이 추가되었다.**  
관련키의 삭제나 수정을 무조건 금지하는 것이 아니라 추가 동작까지 자동으로 처리하여 무결성을 유지하는 기능이다.  

부모 테이블을 변경하면 자식 테이블까지 알아서 수정한다.  
이 기능을 사용하려면 외래키 제약 뒤에 다음 선언을 추가한다.  
```SQL
ON DELETE { NO ACTION | CASCADE | SET NULL | SET DEFAULT }
ON UPDATE { NO ACTION | CASCADE | SET NULL | SET DEFAULT }
```

CASCADE는 참조되는 키와 연결되어 있는 외래키를 자동으로 삭제하거나 변경한다.  
SET NULL이나 SET DEFAULT는 외래키를 NULL이나 기본값으로 변경하는데 외래키가 NULL허용이거나  
기본값이 지정되어 있어야 한다.  
  
TPROJECT 테이블 삭제한 후 다음 명령으로 다시 생성한다.  
이럴 경우 프로젝트가 참조하고 있는 직원 테이블의 데이터가 삭제될 경우 프로젝트의 데이터도 삭제된다.
```SQL
CREATE TABLE TPROJECT {
    ....
    CONSTRAINT FK_EMP FOREIGN KEY(EMPLOYEE) REFERENCES TEMPLOYEE(NAME)
    ON DELETE CASCADE
}
```
  
다음과 같이 ON UPDATE를 추가(**SQL SERVER, MARIADB에서만 가능**)하면 프로젝트 테이블이 참조하고 있는
직원 테이블의 이름 컬럼 값이 변경될 경우 프로젝트 테이블의 외래키 컬럼 값도 변경된다.
```SQL
CREATE TABLE TPROJECT {
    ....
    CONSTRAINT FK_EMP FOREIGN KEY(EMPLOYEE) REFERENCES TEMPLOYEE(NAME)
    ON DELETE CASCADE ON UPDATE CASCADE
}

/* 프로젝트 테이블 EMPLOYEE컬럼이 직원의 NAME 컬럼을 참조하고 있으므로 EMPLOYEE 컬럼 값도 변경됨*/
UPDATE TEMPLOYEE SET NAME = '문사장' WHERE NAME = '문종민' 
```

테이블을 DROP 할때는 참조 무결성 유지를 위해 프로젝트 -> 직원 순으로 실행한다.

# CHECK 제약
CHECK 키워드와 함께 필드값으로 가능한 값을 조건문으로 지정한다.
```sql
CREATE TABLE TCHECKTEST (
    GENDER CHAR(3) NULL CHECK(GENDER = '남' OR GENDER = '여'),
    GRADE INT NULL CHECK (GRADE >= 1 AND GRADE <= 3),
    ORIGIN CHAR(3) NULL CHECK(ORIGIN IN ('동','서','남','북')),
    NAME CHAR(10) NULL CHECK(NAME LIKE '김%')
)
```

# 기본키 제약 - 단일키
ROW 데이터를 식별하기 위해 사용하는 제약조건
```sql
CREATE TABLE TCITY (
    NAME CHAR(10),
    AREA INT NULL,
    POPU INT NULL,
    METRO CHAR(1) NOT NULL,
    REGION CHAR(6) NOT NULL,
    CONSTRAINT TCITY_PK PRIMARY KEY(NAME)
)
```

# 기본키 제약 - 복합키
```sql
CREATE TABLE TCITY (
    NAME CHAR(10),
    REGION CHAR(6) NOT NULL
    AREA INT NULL,
    POPU INT NULL,
    METRO CHAR(1) NOT NULL,
    CONSTRAINT TCITY_PK PRIMARY KEY(NAME, REGION)
)
```
# UNIQUE 제약 - 단일
NULL값을 제외한 중복은 허용하지 않음
```sql
CREATE TABLE TCITY (
    NAME CHAR(10),
    REGION CHAR(6) NOT NULL
    AREA INT NULL,
    POPU INT NULL,
    METRO CHAR(1) NOT NULL,
    CONSTRAINT TCITY_PK PRIMARY KEY(NAME, REGION)
    CONSTRAINT TCITY_UK UNIQUE(POPU)
)
```

# UNIQUE 제약 - 복합
```sql
CREATE TABLE TCITY (
    NAME CHAR(10),
    REGION CHAR(6) NOT NULL
    AREA INT NULL,
    POPU INT NULL,
    METRO CHAR(1) NOT NULL,
    CONSTRAINT TCITY_PK PRIMARY KEY(NAME, REGION)
    CONSTRAINT TCITY_UK UNIQUE(POPU, AREA)
)
```