# DCL (Data Control Language)

> 데이터베이스를 사용할 유저를 생성하고 권한을 제어할 수 있는 명령어의 집합이다.



## 유저와 권한

 일반적으로 회원제 웹사이트를 방문하여 서비스를 이용하려면 먼저 **회원 가입**을 해야 한다. 유저 아이디, 패스워드, 기타 개인정보를 입력하고 약관에 동의하면 회원 가입이 된다. 그리고 유저 아이디와 패스워드로 로그인하면 웹사이트의 서비스를 이용할 수 있게 된다. 

 그러나 영화나 유료 게임과 같은 특정 컨텐츠를 이용하려면 **‘권한이 없다’**라는 메시지를 볼 수 있다. 여기서 유저 아이디와 패스워드를 유저라 할 수 있고, 유료 서비스에 대한 **결재 여부를 권한**이라 할 수 있다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_223.jpg)

오라클과 SQL-Server의 사용자에 대한 아키텍처는 다른면이 많다고 한다. 오라클은 유저를 통해 데이터베이스에 접속하는 방식이다.(아이디와 비밀번호 방식으로 인스턴스에 접속하고, 그에 맞는 스키마에 오브젝트 생성 등의 권한을 부여받게 된다.)

SQL Server는 인스턴스에 접속하기 위해 로그인이라는 것을 생성하게 되며, 인스턴스 내에 존재하는 다수의 데이터베이스에 연결하여 작업하기 위해 유저를 생성한 후 로그인과 유저를 매핑해 주어야 한다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_224.jpg)

### (1) 유저 생성 & 시스템 권한 부여

사용자가 실행하는 모든 DDL 문장(CREATE, ALTER, DROP, RENAME 등)은 그에 해당하는 적절한 권한이 있어야만 문장을 실행할 수 있다. 이걸 시스템 권한이라고 하고 약 100여개의 종류가 있는데 이걸 하나하나 구별해서 지정하기 어렵기 때문에 `롤(ROLE)`을 사용해서 관리한다고 한다.

- CREATE USER

  ```SQL
  # 1. SCOTT 유저로 접속한 다음 PJS 유저(패스워드: KOREA7)를 생성
  
  CONN SCOTT/TIGER 
  * 연결됨 
  
  CREATE USER PJS IDENTIFIED BY KOREA7;
  CREATE USER PJS IDENTIFIED BY KOREA7;
  * 1행에 오류: ERROR: 권한이 불충분하다
  ```

  > 현재 SCOTT 이란 유저는 유저를 생성할 권한을 부여받지 못했기 때문에 권한 불충분 오류가 발생한다. 이 때, 권한을 가지고 있는 SYSTEM유저로 접속해서 다른 유저에게 권한 부여를 할 수 있다.

  ```SQL
  # 2. SCOTT 유저에게 유저생성 권한(CREATE USER)을 부여한 후 다시 PJS 유저를 생성
  GRANT CREATE USER TO SCOTT;
  * 권한이 부여되었다. 
  
  CONN SCOTT/TIGER 
  * 연결되었다. 
  
  CREATE USER PJS IDENTIFIED BY KOREA7; 
  * 사용자가 생성되었다.
  ```

- 생성된 유저로 로그인하기

  ```SQL
  CONN PJS/KOREA7;
  
  * 오류: ERROR: 사용자 PJS는 CREATE SESSION 권한을 가지고 있지 않음; 로그온이 거절되었다.
  ```

  > 유저는 생성되었으나 아무 권한을 받지 못했기 때문에 로그인을 하면 오류가 난다. 유저가 로그인을 하기 위해서는 CREATE SESSION 권한을 부여받아야 한다.

- CREATE SESSION 권한을 부여

  ```SQL
  CONN SCOTT/TIGER 
  * 연결되었다. 
  
  GRANT CREATE SESSION TO PJS; 
  * 권한이 부여되었다. 
  
  CONN PJS/KOREA7 
  *연결되었다.
  ```

- PJS 유저로 테이블을 생성해보기

  ```SQL
  SELECT * 
  FROM TAB; 
  * 선택된 레코드가 없다. 
  
  CREATE TABLE MENU ( MENU_SEQ NUMBER NOT NULL, TITLE VARCHAR2(10) );
  * 1행에 오류: ERROR: 권한이 불충분하다.
  ```

  >  로그인 권한만 부여되었기 때문에 테이블을 생성하려면 테이블 생성 권한(CREATE TABLE)이 불충분하다는 오류가 발생한다.

-  SYSTEM 유저를 통하여 PJS 유저에게 CREATE TABLE 권한을 부여한 후 다시 테이블을 생성

  ```SQL
  CONN SYSTEM/MANAGER 
  * 연결되었다. 
  
  GRANT CREATE TABLE TO PJS;
  * 권한이 부여되었다.
  
  CONN PJS/KOREA7
  * 연결되었다. 
  
  CREATE TABLE MENU ( MENU_SEQ NUMBER NOT NULL, TITLE VARCHAR2(10) );
  * 테이블이 생성되었다.
  ```



### (2) OBJECT에 대한 권한 부여

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_225.jpg)

특정 유저가 소유한 **객체(OBJECT) 권한**에 대해 알아본다. 오브젝트 권한은 특정 오브젝트인 테이블, 뷰 등에 대한 `SELECT`, `INSERT`, `DELETE`, `UPDATE` **작업 명령어**를 의미한다.



