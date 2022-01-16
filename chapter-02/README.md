# 오라클 테이블 스페이스 만들고, 계정 생성 및 권한 부여

테이블 스페이스는 `/u02/tablespace`에 만든다.

## 1. sqlplus 실행
```bash
mkdir /u02/tablespace

sqlplus / as sysdba # sqlplus 접속
```

```sql
-- 테이블 스페이스 용량 증가를 대비해 5M씩 자동증가 설정을 하였다
CREATE TABLESPACE MYTS DATAFILE 
'/u02/tablespace/myts.dbf' SIZE 100M AUTOEXTEND ON NEXT 5M;
```

## 2. 사용자 계정 생성

계정이 사용할 테이블스페이스는 위에서 생성한 MYTS고 임시 테이블 스페이스는 TEMP로 설정하였다.  
아이디는 test이며 비번은 123123이다.
```sql
CREATE USER test IDENTIFIED BY 123123 
DEFAULT TABLESPACE MYTS TEMPORARY TABLESPACE TEMP;
```

## 3. 사용자 권한 

사전 정의된 시스템에서 제공해주는 롤은 CONNECT 롤, RESOURECE 롤, DBA 롤이 있다.

- CONNECT 
  -사용자가 데이터베이스에 접속 가능하도록 하기 위해서 가장 기본적인 시스템 권한 8가지를 묶어 놓음


```sql
-- CONNECT 롤
-- Release 9.0 버전

ALTER SESSION, CREATE CLUSTER, CREATE DATABASE LINK, CREATE SEQUENCE, CREATE SESSION,

CREATE SYNONYM, CREATE[테이블소유자].[테이블명] TO [다른소유자]- Release 11.2 버전

CREATE SESSION
```




- RESOURCE 롤
  - 사용자가 객체(테이블, 뷰, 인덱스)를 생성할 수 있도록 하기 위해서 시스템 권한을 묶어 놓음

```sql
CREATE CLUSTER, CREATE PROCEDURE, CREATE SEQUENCE, CREATE[테이블소유자].[테이블명] TO [다른소유자]
```

- DBA 롤
  - 사용자들이 소유한 데이터베이스 객체를 관리하고 사용자들을 작성하고 변경하고 제거할 수 있도록 하는 모든 권한을 가짐  
  즉, 시스템 자원을 무제한적으로 사용하며 시스템 관리에 필요한 모든 권한을 부여할 수 있는 강력한 권한을 보유한 롤


## 4. 사용자 권한 부여

```sql
-- test 계정에 connect와 resource 롤 부여
GRANT CONNECT, RESOURCE TO test;

-- user01 계정에 dba 롤 부여
GRANT DBA TO user01
```

## 5. 테이블 접근 권한 부여
```sql
GRANT SELECT ON [테이블소유자].[테이블명] TO [다른소유자];
GRANT INSERT ON [테이블소유자].[테이블명] TO [다른소유자];
GRANT DELETE ON [테이블소유자].[테이블명] TO [다른소유자];
GRANT UPDATE ON [테이블소유자].[테이블명] TO [다른소유자];

-- 또는
GRANT SELECT, INSERT, DELETE, UPDATE ON [테이블소유자].[테이블명] TO [다른소유자];
```

## 6. 읽어 볼만한 것 - [tnsnames.ora과 listener.ora의 역할](https://blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=turtle0788&logNo=40092085230)
