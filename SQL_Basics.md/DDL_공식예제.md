# DDL 예제문제

1. 테이블 생성 예시

   ```
   테이블명 : PLAYER 테이블 설명 : K-리그 선수들의 정보를 가지고 있는 테이블 칼럼명 : PLAYER_ID (선수ID) 문자 고정 자릿수 7자리,PLAYER_NAME (선수명) 문자 가변 자릿수 20자리,TEAM_ID (팀ID) 문자 고정 자릿수 3자리,E_PLAYER_NAME (영문선수명) 문자 가변 자릿수 40자리,NICKNAME (선수별명) 문자 가변 자릿수 30자리,JOIN_YYYY (입단년도) 문자 고정 자릿수 4자리,POSITION (포지션) 문자 가변 자릿수 10자리,BACK_NO (등번호) 숫자 2자리,NATION (국적) 문자 가변 자릿수 20자리,BIRTH_DATE (생년월일) 날짜,SOLAR (양/음) 문자 고정 자릿수 1자리,HEIGHT (신장) 숫자 3자리,WEIGHT (몸무게) 숫자 3자리, 제약조건 : 기본키(PRIMARY KEY) → PLAYER_ID (제약조건명은 PLAYER_ID_PK) 값이 반드시 존재 (NOT NULL) → PLAYER_NAME, TEAM_ID
   ```

   ```sql
   [예제] Oracle CREATE TABLE PLAYER ( PLAYER_ID CHAR(7) NOT NULL, PLAYER_NAME VARCHAR2(20) NOT NULL, TEAM_ID CHAR(3) NOT NULL, E_PLAYER_NAME VARCHAR2(40), NICKNAME VARCHAR2(30), JOIN_YYYY CHAR(4), POSITION VARCHAR2(10), BACK_NO NUMBER(2), NATION VARCHAR2(20), BIRTH_DATE DATE, SOLAR CHAR(1), HEIGHT NUMBER(3), WEIGHT NUMBER(3), CONSTRAINT PLAYER_PK PRIMARY KEY (PLAYER_ID), CONSTRAINT PLAYER_FK FOREIGN KEY (TEAM_ID) REFERENCES TEAM(TEAM_ID) ); 테이블이 생성되었다.
   ```

2. 제약조건 예제

   ```sql
   테이블명 : TEAM 테이블 설명 : K-리그 선수들의 소속팀에 대한 정보를 가지고 있는 테이블 칼럼명 : TEAM_ID (팀 고유 ID) 문자 고정 자릿수 3자리,REGION_NAME (연고지 명) 문자 가변 자릿수 8자리,TEAM_NAME (한글 팀 명) 문자 가변 자릿수 40자리,E-TEAM_NAME (영문 팀 명) 문자 가변 자릿수 50자리 ,ORIG_YYYY (창단년도) 문자 고정 자릿수 4자리,STADIUM_ID (구장 고유 ID) 문자 고정 자릿수 3자리,ZIP_CODE1 (우편번호 앞 3자리) 문자 고정 자릿수 3자리,ZIP_CODE2 (우편번호 뒷 3자리) 문자 고정 자릿수 3자리,ADDRESS (주소) 문자 가변 자릿수 80자리,DDD (지역번호) 문자 가변 자릿수 3자리,TEL (전화번호) 문자 가변 자릿수 10자리,FAX (팩스번호) 문자 가변 자릿수 10자리,HOMEPAGE (홈페이지) 문자 가변 자릿수 50자리OWNER (구단주) 문자 가변 자릿수 10자리, 제약조건 : 기본 키(PRIMARY KEY) → TEAM_ID (제약조건명은 TEAM_ID_PK) NOT NULL → REGION_NAME, TEAM_NAME, STADIUM_ID (제약조건명은 미적용)
   ```

   ```sql
   [예제] Oracle CREATE TABLE TEAM ( TEAM_ID CHAR(3) NOT NULL, REGION_NAME VARCHAR2(8) NOT NULL, TEAM_NAME VARCHAR2(40) NOT NULL, E_TEAM_NAME VARCHAR2(50), ORIG_YYYY CHAR(4), STADIUM_ID CHAR(3) NOT NULL, ZIP_CODE1 CHAR(3), ZIP_CODE2 CHAR(3), ADDRESS VARCHAR2(80), DDD VARCHAR2(3), TEL VARCHAR2(10), FAX VARCHAR2(10), HOMEPAGE VARCHAR2(50), OWNER VARCHAR2(10), CONSTRAINT TEAM_PK PRIMARY KEY (TEAM_ID), CONSTRAINT TEAM_FK FOREIGN KEY (STADIUM_ID) REFERENCES STADIUM(STADIUM_ID) ); 테이블이 생성되었다.
   ```

3. 생성된 테이블 구조 확인

   ```sql
   [실행 결과] Oracle DESCRIBE PLAYER; 칼럼 NULL 가능 데이터 유형 ------------------ ----------- -------------- PLAYER_ID NOT NULL CHAR(7) PLAYER_NAME NOT NULL VARCHAR2(20) TEAM_ID NOT NULL CHAR(3) E_PLAYER_NAME VARCHAR2(40) NICKNAME VARCHAR2(30) JOIN_YYYY CHAR(4) POSITION VARCHAR2(10) BACK_NO NUMBER(2) NATION VARCHAR2(20) BIRTH_DATE DATE SOLAR CHAR(1) HEIGHT NUMBER(3) WEIGHT NUMBER(3)
   ```

4. SELECT 문장을 통한 테이블 생성 사례

   ```sql
   [예제] Oracle CREATE TABLE TEAM_TEMP AS SELECT * FROM TEAM; 테이블이 생성되었다.
   
   ```

   ```sql
   [실행 결과] Oracle DESC TEAM_TEMP; 칼럼 NULL 가능 데이터 유형 -------------- -------- ----------- TEAM_ID NOT NULL CHAR(3) REGION_NAME NOT NULL VARCHAR2(4) TEAM_NAME NOT NULL VARCHAR2(40) E_TEAM_NAME VARCHAR2(50) ORIG_YYYY CHAR(4) STADIUM_ID NOT NULL CHAR(3) ZIP_CODE1 CHAR(3) ZIP_CODE2 CHAR(3) ADDRESS VARCHAR2(80) DDD VARCHAR2(3) TEL VARCHAR2(10) FAX VARCHAR2(10) HOMEPAGE VARCHAR2(50) OWNER VARCHAR2(10)
   ```

5. ALTER TABLE - ADD COLUMN

   ```SQL
   ALTER TABLE 테이블명 ADD 추가할 칼럼명 데이터 유형;
   ```

   ```SQL
   [예제] Oracle ALTER TABLE PLAYER ADD (ADDRESS VARCHAR2(80)); 테이블이 변경되었다.
   ```

   ```SQL
   [실행 결과] Oracle DESC PLAYER; 칼럼 NULL 가능 데이터 유형 -------------- ---------- ------------- PLAYER_ID NOT NULL CHAR(7) PLAYER_NAME NOT NULL VARCHAR2(20) TEAM_ID NOT NULL CHAR(3) E_PLAYER_NAME VARCHAR2(40) NICKNAME VARCHAR2(30) JOIN_YYYY CHAR(4) POSITION VARCHAR2(10) BACK_NO NUMBER(2) NATION VARCHAR2(20) BIRTH_DATE DATE SOLAR CHAR(1) HEIGHT NUMBER(3) WEIGHT NUMBER(3) ADDRESS VARCHAR2(80) ☜ 추가된 열
   ```

6. ALTER TABLE - DROP COLUMN

   ```sql
   ALTER TABLE 테이블명 DROP COLUMN 삭제할 칼럼명;
   ```

   ```SQL
   [예제] Oracle ALTER TABLE PLAYER DROP COLUMN ADDRESS; 테이블이 변경되었다.
   ```

   ```SQL
   [예제] SQL Server ALTER TABLE PLAYER DROP COLUMN ADDRESS; 명령이 완료되었다.
   ```

   ```sql
   [실행 결과] Oracle DESC PLAYER; 칼럼 NULL 가능 데이터 유형 -------------- --------- ----------- PLAYER_ID NOT NULL CHAR(7) PLAYER_NAME NOT NULL VARCHAR2(20) TEAM_ID NOT NULL CHAR(3) E_PLAYER_NAME VARCHAR2(40) NICKNAME VARCHAR2(30) JOIN_YYYY CHAR(4) POSITION VARCHAR2(10) BACK_NO NUMBER(2) NATION VARCHAR2(20) BIRTH_DATE DATE SOLAR CHAR(1) HEIGHT NUMBER(3) WEIGHT NUMBER(3)
   ```

7. ALTER TABLE - MODIFY COLUMN

   ```SQL
   [Oracle] ALTER TABLE 테이블명 MODIFY (칼럼명1 데이터 유형 [DEFAULT 식] [NOT NULL], 칼럼명2 데이터 유형 …);
   ```

   ```sql
   [예제] Oracle ALTER TABLE TEAM_TEMP MODIFY (ORIG_YYYY VARCHAR2(8) DEFAULT '20020129' NOT NULL); 테이블이 변경되었다.
   ```

   ```SQL
   [실행 결과] Oracle DESC TEAM_TEMP; 칼럼 NULL 가능 데이터 유형 ----------------- --------- ---------- TEAM_ID NOT NULL CHAR(3) REGION_NAME NOT NULL VARCHAR2(4) TEAM_NAME NOT NULL VARCHAR2(40) E_TEAM_NAME VARCHAR2(50) ORIG_YYYY NOT NULL VARCHAR2(8) ☜ 기본값 '20020129' STADIUM_ID NOT NULL CHAR(3) ZIP_CODE1 CHAR(3) ZIP_CODE2 CHAR(3) ADDRESS VARCHAR2(80) DDD VARCHAR2(3) TEL VARCHAR2(10) FAX VARCHAR2(10) HOMEPAGE VARCHAR2(50) OWNER VARCHAR2(10)
   ```

8. ALTER TABLE - RENAME COLUMN

   ```sql
   ALTER TABLE 테이블명 RENAME COLUMN 변경해야 할 칼럼명 TO 새로운 칼럼명;
   ```

   > RENAME COLUMN으로 칼럼명이 변경되면, 해당 칼럼과 관계된 제약조건에 대해서도 자동으로 변경되는 장점이 있지만, ADD/DROP COLUMN 기능처럼 ANSI/ISO에 명시되어 있는 기능이 아니고 Oracle 등 일부 DBMS에서만 지원하는 기능이다.

9. DROP CONSTRAINT

   ```SQL
   ALTER TABLE 테이블명 DROP CONSTRAINT 제약조건명;
   ```

   ```SQL
   [예제] PLAYER 테이블의 외래키 제약조건을 삭제한다.
   
   [예제] Oracle ALTER TABLE PLAYER DROP CONSTRAINT PLAYER_FK; 테이블이 변경되었다.
   [예제] SQL Server ALTER TABLE PLAYER DROP CONSTRAINT PLAYER_FK; 명령이 완료되었다.
   ```

10. ADD CONSTRAINT

    ```SQL
    ALTER TABLE 테이블명 ADD CONSTRAINT 제약조건명 제약조건 (칼럼명);
    ```

    ```sql
    [예제] PLAYER 테이블에 TEAM 테이블과의 외래키 제약조건을 추가한다. 제약조건명은 PLAYER_FK로 하고, PLAYER 테이블의 TEAM_ID 칼럼이 TEAM 테이블의 TEAM_ID를 참조하는 조건이다.
    
    [예제] Oracle ALTER TABLE PLAYER ADD CONSTRAINT PLAYER_FK FOREIGN KEY (TEAM_ID) REFERENCES TEAM(TEAM_ID); 테이블이 변경되었다.
    ```

    ```sql
    [예제] PLAYER 테이블이 참조하는 TEAM 테이블을 제거해본다.
    
    [예제] Oracle DROP TABLE TEAM; ERROR: 외래 키에 의해 참조되는 고유/기본 키가 테이블에 있다. ※ 테이블은 삭제되지 않음
    ```

    ```sql
    [예제] PLAYER 테이블이 참조하는 TEAM 테이블의 데이터를 삭제해본다.
    
    [예제] Oracle DELETE TEAM WHERE TEAM_ID = 'K10'; ERROR: 무결성 제약조건(SCOTT.PLAYER_FK)이 위배되었다. 자식 레코드가 발견되었다. ※ 데이터는 삭제되지 않음
    ```

    

11. RENAME TABLE

    ```sql
    RENAME 변경전 테이블명 TO 변경후 테이블명;
    ```

    ```SQL
    [예제] RENAME 문장을 이용하여 TEAM 테이블명을 다른 이름으로 변경하고, 다시 TEAM 테이블로 변경한다.
    
    [예제] Oracle RENAME TEAM TO TEAM_BACKUP; 테이블 이름이 변경되었다. RENAME TEAM_BACKUP TO TEAM; 테이블 이름이 변경되었다.
    ```

12. DROP TABLE

    ```SQL
    DROP TABLE 테이블명 [CASCADE CONSTRAINT];
    ```

    ```SQL
    [예제] PLAYER 테이블을 제거한다.
    
    [예제] Oracle DROP TABLE PLAYER; 테이블이 삭제되었다. DESC PLAYER; ERROR: 설명할 객체를 찾을 수 없다.
    ```

13. TRUNCATE TABLE

    ```SQL
    TRUNCATE TABLE PLAYER;
    ```

    ```SQL
    [예제] TRUNCATE TABLE을 사용하여 해당 테이블의 모든 행을 삭제하고 테이블 구조를 확인한다.
    
    [예제] Oracle TRUNCATE TABLE TEAM; 테이블이 트렁케이트되었다.
    ```

    ```sql
    [실행 결과] Oracle DESC TEAM; 칼럼 NULL 가능 데이터 유형 ----------------- ---------- --------- TEAM_ID NOT NULL CHAR(3) REGION_NAME NOT NULL VARCHAR2(4) TEAM_NAME NOT NULL VARCHAR2(40) E_TEAM_NAME VARCHAR2(50) ORIG_YYYY CHAR(4) STADIUM_ID NOT NULL CHAR(3) ZIP_CODE1 CHAR(3) ZIP_CODE2 CHAR(3) ADDRESS VARCHAR2(80) DDD VARCHAR2(3) TEL VARCHAR2(10) FAX VARCHAR2(10) HOMEPAGE VARCHAR2(50) OWNER VARCHAR2(10)
    ```

    ```sql
    [예제] DROP TABLE을 사용하여 해당 테이블을 제거하고 테이블 구조를 확인한다.
    
    [예제] Oracle DROP TABLE TEAM; 테이블이 삭제되었다. DESC TEAM; ERROR: 설명할 객체를 찾을 수 없다.
    ```

    

