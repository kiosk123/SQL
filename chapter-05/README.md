# 외래키 제약

직원과 프로젝트 관계는 참조 관계다
```sql
CREATE TABLE tEmployee
(
	name CHAR(10) PRIMARY KEY,
	salary INT NOT NULL,
	addr VARCHAR(30) NOT NULL
);

INSERT INTO tEmployee VALUES ('김상형', 650, '이천시');
INSERT INTO tEmployee VALUES ('문종민', 480, '대구시');
INSERT INTO tEmployee VALUES ('권성직', 625, '안동시');

/* 프로젝트 테이블은 직원 테이블을 참조하고 있다.*/
CREATE TABLE tProject
(
	projectID INT PRIMARY KEY,
	employee CHAR(10) NOT NULL,
	project VARCHAR(30) NOT NULL,
	cost INT,
    /* 외래키 참조 설정  - 프로젝트의 employee 컬럼이 tEmployee 테이블의 name을 참조하고 있음*/
    CONSTRAINT FK_emp FOREIGN KEY(employee) REFERENCES tEmployee(name) 
);

INSERT INTO tProject VALUES (1, '김상형', '홍콩 수출건', 800);
INSERT INTO tProject VALUES (2, '김상형', 'TV 광고건', 3400);
INSERT INTO tProject VALUES (3, '김상형', '매출분석건', 200);
INSERT INTO tProject VALUES (4, '문종민', '경영 혁신안 작성', 120);
INSERT INTO tProject VALUES (5, '문종민', '대리점 계획', 85);
INSERT INTO tProject VALUES (6, '권성직', '노조 협상건', 24);
INSERT INTO tProject VALUES (7, '홍길동', '원자재 매입', 900);
```

위와 같이 외래키 설정을 하면 insert시 직원 -> 프로젝트 테이블 순으로 해야되고  
반대로 delete식 프로젝트 -> 직원 테이블 순으로 해야된다.  
  
외래키 제약이 설정되어 있는 상태에서 `drop table tEmployee`를 실행하면 에러가 발생한다.  
  
외래키 제약이 너무 강력해서 가끔 불편할 때가 있다. 직원을 삭제하려면 이 직원이 담당한 프로젝트를 먼저 삭제하는  
단계를 거쳐야 한다. 실수를 방지하기 위한 제약이지만 진짜 지워야 하는 경우가 빈번하다면 매번 순서대로 작업하기 번거롭다.  
  
그래서 **관련 작업을 자동화 하여 한 번에 처리하는 연계 참조 무결성 제약이 추가되었다.**  
관련키의 삭제나 수정을 무조건 금지하는 것이 아니라 추가 동작까지 자동으로 처리하여 무결성을 유지하는 기능이다.  

부모 테이블을 변경하면 자식 테이블까지 알아서 수정한다.  
이 기능을 사용하려면 외래키 제약 뒤에 다음 선언을 추가한다.  
```sql
ON DELETE { NO ACTION | CASCADE | SET NULL | SET DEFAULT }
ON UPDATE { NO ACTION | CASCADE | SET NULL | SET DEFAULT }
```

CASCADE는 참조되는 키와 연결되어 있는 외래키를 자동으로 삭제하거나 변경한다.  
SET NULL이나 SET DEFAULT는 외래키를 NULL이나 기본값으로 변경하는데 외래키가 NULL허용이거나  
기본값이 지정되어 있어야 한다.  
  
tProject 테이블 삭제한 후 다음 명령으로 다시 생성한다.  
이럴 경우 프로젝트가 참조하고 있는 직원 테이블의 데이터가 삭제될 경우 프로젝트의 데이터도 삭제된다.
```sql
CREATE TABLE tProject {
    ....
    CONSTRAINT FK_emp FOREIGN KEY(employee) REFERENCES tEmployee(name)
    ON DELETE CASCADE
}
```
  
다음과 같이 ON UPDATE를 추가(**SQL Server, MariaDB에서만 가능**)하면 프로젝트 테이블이 참조하고 있는
직원 테이블의 이름 컬럼 값이 변경될 경우 프로젝트 테이블의 외래키 컬럼 값도 변경된다.
```sql
CREATE TABLE tProject {
    ....
    CONSTRAINT FK_emp FOREIGN KEY(employee) REFERENCES tEmployee(name)
    ON DELETE CASCADE ON UPDATE CASCADE
}

/* 프로젝트 테이블 employee컬럼이 직원의 name 컬럼을 참조하고 있으므로 employee 컬럼 값도 변경됨*/
UPDATE tEmployee SET name = '문사장' WHERE name = '문종민' 
```

테이블을 drop 할때는 참조 무결성 유지를 위해 프로젝트 -> 직원 순으로 실행한다.


