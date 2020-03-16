# 절차형 SQL

> 절차 지향적인 프로그램이 가능하도록 오라클에서는 PL(Procedural Language)/SQL, SQL-Server에서는 T-SQL 로 절차형 SQL을 제공하고 있다.

절차형 SQL을 이용하면 SQL문의 **연속적인 실행**이나 조건에 따른 **분기처리**를 이용하여 특정 기능을 수행하는 저장 모듈을 생성할 수 있다. 이 정리본에서는 절차형 SQL을 이용하여 만들 수 있는 저장 모듈인 Procedure, User Defined Function, Trigger에 대해서 간단하게 살펴본다. 

## PL / SQL

Oracle의 PL/SQL은 **Block 구조**로 되어있고 Block 내에는 **DML 문장**과 **QUERY 문장**, 그리고 **절차형 언어(IF, LOOP)** 등을 사용할 수 있으며, 절차적 프로그래밍을 가능하게 하는 트랜잭션 언어이다. 

이런 PL/SQL을 이용하여 다양한 `저장 모듈(Stored Module)`을 개발할 수 있다. 

- `저장 모듈(Stored Module)` : PL/SQL 문장을 데이터베이스 서버에 저장하여 사용자와 애플리케이션 사이에서 공유할 수 있도록 만든 일종의 SQL 컴포넌트 프로그램

  ( Oracle의 저장 모듈에는 Procedure, User Defined Function, Trigger가 있다.)



### PL/SQL의 특징

- Block 구조로 되어있어 각 기능별로 모듈화가 가능
- 변수, 상수 등을 선언하여 SQL 문장 간 값을 교환
- IF, LOOP 등의 절차형 언어를 사용하여 절차적인 프로그램이 가능
- DBMS 정의 에러나 사용자 정의 에러를 정의하여 사용할 수 있다.
- PL/SQL은 Oracle에 내장되어 있으므로 Oracle과 PL/SQL을 지원하는 어떤 서버로도 프로그램을 옮길 수 있다.
- PL/SQL은 응용 프로그램의 성능을 향상시킨다.
- PL/SQL은 여러 SQL 문장을 Block으로 묶고 한 번에 Block 전부를 서버로 보내기 때문에 통신량을 줄일 수 있다.



### PL/SQL의 구조

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_231.jpg)

- `DECLARE` : BEGIN ~ END 절에서 사용될 변수와 인수에 대한 정의 및 데이터 타입을 선언하는 선언부이다.
-  `BEGIN ~ END` : 개발자가 처리하고자 하는 SQL문과 여러 가지 비교문, 제어문을 이용하여 필요한 로직을 처리하는 실행부이다. 
- `EXCEPTION` : BEGIN ~ END 절에서 실행되는 SQL문이 실행될 때 에러가 발생하면 그 에러를 어떻게 처리할 것이지를 정의하는 예외 처리부이다.



### 기본 문법(Syntax)

- 기본문법

  ```sql
  CREATE [OR REPLACE] Procedure [Procedure_name] ( argument1 [mode] data_type1, argument2 [mode] date_type2, ... ... ) 
  IS [AS] ... ... BEGIN ... ... EXCEPTION ... ... END; /
  ```

  - CREATE TABLE 명령어로 테이블을 생성하듯 **CREATE 명령어로** 데이터베이스 내에 **프로시저를 생성**할 수 있다. 
  - **[OR REPLACE] 절**은 데이터베이스 내에 같은 이름의 프로시저가 있을 경우, 기존의 프로시저를 무시하고 **새로운 내용으로 덮어쓰기** 하겠다는 의미이다.
  -  **Argument**는 프로시저가 호출될 때 프로시저 안으로 어떤 값이 들어오거나 혹은 프로시저에서 처리한 결과값을 운영 체제로 **리턴시킬 매개 변수**를 지정할 때 사용한다.
  - **[mode] 부분**에 지정할 수 있는 매개 변수의 유형은 3가지가 있다. 먼저 IN은 운영 체제에서 프로시저로 전달될 변수의 MODE이고, OUT은 프로시저에서 처리된 결과가 운영체제로 전달되는 MODE이다.
  - 마지막에 있는 **슬래쉬(“/”)**는 데이터베이스에게 프로시저를 **컴파일**하라는 명령어이다.

- 생성된 프로시저 삭제 명령어

  ```sql
  DROP Procedure [Procedure_name];
  ```

  

## T-SQL

> T-SQL은 근본적으로 SQL Server를 제어하기 위한 언어이다.



- 변수 선언 기능 @@이라는 전역변수(시스템 함수)와 @이라는 지역변수가 있다.
- 지역변수는 사용자가 자신의 연결 시간 동안만 사용하기 위해 만들어지는 변수이며 전역변수는 이미 SQL서버에 내장된 값이다.
- 데이터 유형(Data Type)을 제공한다. 즉 int, float, varchar 등의 자료형을 의미한다. 
- 연산자(Operator) 산술연산자( +, -, *, /)와 비교연산자(=, <, >, <>) 논리연산자(and, or, not) 사용이 가능하다. 
- 흐름 제어 기능 IF-ELSE와 WHILE, CASE-THEN 사용이 가능하다.
- 주석 기능한줄 주석 : -- 뒤의 내용은 주석범위 주석 : /* 내용 */ 형태를 사용하며, 여러 줄도 가능함



### T-SQL의 구조

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_232.jpg)

> 대체적으로 PL/SQL과 유사하다.

- `DECLARE` : BEGIN ~ END 절에서 사용될 변수와 인수에 대한 정의 및 데이터 타입을 선언하는 선언부이다.
- `BEGIN ~ END` : 개발자가 처리하고자 하는 SQL문과 여러 가지 비교문, 제어문을 이용하여 필요한 로직을 처리하는 실행부이다. T-SQL에서는 BEGIN, END 문을 반드시 사용해야하는 것은 아니지만 블록 단위로 처리하고자 할 때는 반드시 작성해야 한다.
- `ERROR 처리` : BEGIN ~ END 절에서 실행되는 SQL문이 실행될 때 에러가 발생하면 그 에러를 어떻게 처리할 것이지를 정의하는 예외 처리부이다.



### 기본문법(Syntax)

- 기본 문법

  ```sql
  CREATE Procedure [schema_name.]Procedure_name @parameter1 data_type1 [mode], @parameter2 date_type2 [mode], ... ... WITH AS ... ... BEGIN ... ... ERROR 처리 ... ... END;
  ```

  - **프로시저의 변경**이 필요할 경우 Oracle은 [CREATE OR REPLACE]와 같이 하나의 구문으로 처리하지만 SQL Server는 **CREATE 구문을 ALTER 구문으로** 변경하여야 한다. 
  - @parameter는 프로시저가 호출될 때 프로시저 안으로 어떤 값이 들어오거나 혹은 프로시저에서 처리한 결과 값을 리턴 시킬 매개 변수를 지정할 때 사용한다.
  - [mode] 부분에 지정할 수 있는 매개 변수(@parameter)의 유형은 4가지가 있다.
    - VARYING결과 집합이 출력 매개 변수로 사용되도록 지정합니다. CURSOR 매개변수에만 적용된다.
    - DEFAULT지정된 매개변수가 프로시저를 호출할 당시 지정되지 않을 경우 지정된 기본값으로 처리한다. 즉, 기본 값이 지정되어 있으면 해당 매개 변수를 지정하지 않아도 프로시저가 지정된 기본 값으로 정상적으로 수행이 된다.
    - OUT, OUTPUT프로시저에서 처리된 결과 값을 EXECUTE 문 호출 시 반환한다.
    - READONLY자주 사용되지는 않는다. 프로시저 본문 내에서 매개 변수를 업데이트하거나 수정할 수 없음을 나타낸다. 매개 변수 유형이 사용자 정의 테이블 형식인 경우 READONLY를 지정해야 한다.
  - WITH 부분에 지정할 수 있는 옵션 3가지
    -  RECOMPILE데이터베이스 엔진에서 현재 프로시저의 계획을 캐시하지 않고 프로시저가 런타임에 컴파일 된다. 
    - ENCRYPTIONCREATE PROCEDURE 문의 원본 텍스트가 알아보기 어려운 형식으로 변환된다. 변조된 출력은 SQL Server의 카탈로그 뷰 어디에서도 직접 표시되지 않는다. 원본을 볼 수 있는 방법이 없기 때문에 반드시 원본은 백업을 해두어야 한다.
    - EXECUTE AS 해당 저장 프로시저를 실행할 보안 컨텍스트를 지정한다.

- 생성된 프로시저 삭제하는 명령어

  ```sql
  DROP Procedure [schema_name.]Procedure_name;
  ```

  

## Procedure의 생성과 활용

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_233.jpg)

> 앞으로 생성할 Procedure의 기능을 Flow Chart로 나타낸 그림



#### 예시로 만든 유저(SCOTT)가 소유한 DEPT 테이블 구조

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_234.jpg)

```SQL
CREATE OR REPLACE Procedure p_DEPT_insert 

-------------① ( v_DEPTNO in number, v_dname in varchar2, v_loc in varchar2, v_result out varchar2) IS cnt number := 0; BEGIN SELECT COUNT(*) INTO CNT -------------② FROM DEPT WHERE DEPTNO = v_DEPTNO AND ROWNUM = 1; if cnt > 0 then -------------③ v_result := '이미 등록된 부서번호이다'; else INSERT INTO DEPT (DEPTNO, DNAME, LOC) -------------④ VALUES (v_DEPTNO, v_dname, v_loc); COMMIT; -------------⑤ v_result := '입력 완료!!'; end if; EXCEPTION -------------⑥ WHEN OTHERS THEN ROLLBACK; v_result := 'ERROR 발생'; END; /
```

1. DEPT 테이블에 들어갈 칼럼 값(부서코드, 부서명, 위치)을 입력 받는다.
2. 입력 받은 부서코드가 존재하는지 확인한다.
3. 부서코드가 존재하면 '이미 등록된 부서번호입니다'라는 메시지를 출력 값에 넣는다.
4. 부서코드가 존재하지 않으면 입력받은 필드 값으로 새로운 부서 레코드를 입력한다.
5. 새로운 부서가 정상적으로 입력됐을 경우에는 COMMIT 명령어를 통해서 트랜잭션을 종료한다.
6. 에러가 발생하면 모든 트랜잭션을 취소하고 'ERROR 발생'라는 메시지를 출력값에 넣는다.



#### PROCEDURE 작성 시 주의할 문법 요소

1. PL/SQL 및 T-SQL에서는 다양한 변수가 있다. (예제에서 나온 cnt라는 변수를 `SCALAR 변수`라고 한다.)
   - `SCALAR 변수` : 사용자의 임시 데이터를 하나만 저장할 수 있는 변수이며 거의 모든 형태의 데이터 유형을 지정할 수 있다.
2. PL/SQL에서 사용하는 SQL 구문은 대부분 지금까지 살펴본 것과 동일하게 사용할 수 있지만 SELECT 문장은 다르다. PL/SQL에서 사용하는 SELECT 문장은 결과값이 반드시 있어야 하며, 그 결과 역시 반드시 하나여야 한다. 조회 결과가 없거나 하나 이상인 경우에는 에러를 발생시킨다.
3.  T-SQL을 비롯하여 일반적으로 대입 연산자는 “=”을 사용하지만 PL/SQL에서는 “:=”를 사용한다. 
4. 에러 처리를 담당하는 EXCEPTION에는 WHEN ~ THEN 절을 사용하여 에러의 종류별로 적절히 처리한다. OTHERS를 이용하여 모든 에러를 처리할 수 있지만 정확하게 에러를 처리하는 것이 좋다.



## User Defined Function의 생성과 활용

> User Defined Function은 Procedure처럼 절차형 SQL을 로직과 함께 데이터베이스 내에 저장해 놓은 명령문의 집합을 의미한다. 



#### 예제코드

```SQL
SELECT SCHE_DATE 경기일자,
       HOMETEAM_ID || ' - ' || AWAYTEAM_ID 팀들, 
       HOME_SCORE || ' - ' || AWAY_SCORE SCORE, 
       ABS(HOME_SCORE - AWAY_SCORE) 점수차 
FROM SCHEDULE 
WHERE GUBUN = 'Y' AND SCHE_DATE BETWEEN '20120801' AND '20120831'
ORDER BY SCHE_DATE;
```

> K-리그 8월 경기결과와 두 팀간의 점수차를 ABS 함수를 사용하여 절대값으로 출력

```SQL
[예제] Oracle CREATE OR REPLACE Function UTIL_ABS (v_input in number) 
---------------- ① return NUMBER IS v_return number := 0; 
---------------- ② BEGIN if v_input < 0 then 
---------------- ③ v_return := v_input * -1; else v_return := v_input; end if; RETURN v_return; 
---------------- ④ END; /
```

> [예제]에서 사용한 ABS 함수를 만드는데, INPUT 값으로 숫자만 들어온다고 가정한다.

- UTIL_ABS Function의 처리 과정

  1. 숫자 값을 입력 받는다. 예제에서는 숫자 값만 입력된다고 가정한다.
  2. 리턴 값을 받아 줄 변수인 v_return를 선언한다.
  3. 입력 값이 음수이면 -1을 곱하여 v_return 변수에 대입한다.
  4. v_return 변수를 리턴한다.

  

## Trigger의 생성과 활용

> Trigger란 특정한 테이블에 INSERT, UPDATE, DELETE와 같은 DML문이 수행되었을 때, 데이터베이스에서 자동으로 동작하도록 작성된 프로그램이다.

* 데이터 베이스에서 자동 동작?

  즉,  사용자가 직접 호출하는 것이 아니고 데이터베이스에서 자동적으로 수행하게 된다.

* Trigger는 테이블과 뷰, 데이터베이스 작업을 대상으로 정의할 수 있으며, **전체 트랜잭션 작업**에 대해 발생되는 Trigger와 **각 행에 대해서 발생**되는 Trigger가 있다.

  ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_235.jpg)

  > 트리거(Trigger)를 사용하여 주문한 건이 입력될 때마다, 일자별 상품별로 판매수량과 판매금액을 집계하여 집계자료를 보관하도록 한다.

  ```sql
  CREATE TABLE ORDER_LIST ( 
                           ORDER_DATE CHAR(8) NOT NULL, 
                           PRODUCT VARCHAR2(10) NOT NULL, 
                           QTY NUMBER NOT NULL,
                           AMOUNT NUMBER NOT NULL)
  ;
                           
  CREATE TABLE SALES_PER_DATE (
                               SALE_DATE CHAR(8) NOT NULL,
                               PRODUCT VARCHAR2(10) NOT NULL,
                               QTY NUMBER NOT NULL,
                               AMOUNT NUMBER NOT NULL)
  ;
  ```

  > 두 개의 테이블을 생성했다.

* 이제 Trigger를 작성한다.

*  Trigger의 역할은 ORDER_LIST에 주문 정보가 입력되면 주문 정보의 주문 일자(ORDER_LIST.ORDER_DATE)와 주문 상품(ORDER_LIST.PRODUCT)을 기준으로 판매 집계 테이블(SALES_PER_DATE)에 해당 주문 일자의 주문 상품 레코드가 존재하면 판매 수량과 판매 금액을 더하고 존재하지 않으면 새로운 레코드를 입력한다.

  ```sql
  CREATE OR REPLACE Trigger SUMMARY_SALES 
  
  ---------------- ① AFTER INSERT ON ORDER_LIST FOR EACH ROW DECLARE 
  ---------------- ② o_date ORDER_LIST.order_date%TYPE; o_prod ORDER_LIST.product%TYPE; BEGIN o_date := :NEW.order_date; o_prod := :NEW.product; UPDATE SALES_PER_DATE 
  ---------------- ③ SET qty = qty + :NEW.qty, amount = amount + :NEW.amount WHERE sale_date = o_date AND product = o_prod; if SQL%NOTFOUND then 
  ---------------- ④ INSERT INTO SALES_PER_DATE VALUES(o_date, o_prod, :NEW.qty, :NEW.amount); end if; END; /
  ```

  - Trigger를 선언한다.CREATE OR REPLACE Trigger SUMMARY_SALES : Trigger 선언문AFTER INSERT : 레코드가 입력이 된 후 Trigger 발생 ON ORDER_LIST : ORDER_LIST 테이블에 Trigger 설정FOR EACH ROW : 각 ROW마다 Trigger 적용
  - o_date(주문일자), o_prod(주문상품) 값을 저장할 변수를 선언하고, 신규로 입력된 데이터를 저장한다. : NEW는 신규로 입력된 레코드의 정보를 가지고 있는 구조체 : OLD는 수정, 삭제되기 전의 레코드를 가지고 있는 구조체 [표 Ⅱ-2-17] 참조
  - 먼저 입력된 주문 내역의 주문 일자와 주문 상품을 기준으로 SALES_PER_DATE 테이블에 업데이트한다.
  - 처리 결과가 SQL%NOTFOUND이면 해당 주문 일자의 주문 상품 실적이 존재하지 않으며, SALES_ PER_DATE 테이블에 새로운 집계 데이터를 입력한다.

  

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_236.jpg)

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_237.jpg)



## 프로시저와 트리거의 차이점

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_238.jpg)

프로시저는 BEGIN ~ END 절 내에 COMMIT, ROLLBACK과 같은 트랜잭션 종료 명령어를 사용할 수 있지만, 데이터베이스 트리거는 BEGIN ~ END 절 내에 사용할 수 없다.

