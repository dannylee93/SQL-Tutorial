# 관계형 데이터베이스

수 많은 사용자들에게 효율적인 데이터 관리와 예상치 못한 사건으로 인한 데이터 손상을 막고, 필요하다면 데이터 복구까지 할 수 있는 이런 요구사항을 만족시켜주는 것이 DBMS(Database Management System) 라고 한다.



#### 관계형 데이터베이스(Relational Database)

만약 하나의 파일을 여러 지사에서 복사해 나눠가진 다음, 변경작업을 하면 이 여러 개의 변경된 파일은 결국 데이터의 불 일치성이 생긴다. 그래서 관계형 데이터베이스는 **정규화를 통해 합리적인 테이블 모델링으로 이상현상을 제거하고 많은 사용자들이 동시에 데이터를 공유 또는 조작할 수** 있게 구현 해준다.



#### SQL(Structured Query Language)

관계형 데이터베이스에서 데이터 정의, 데이터 조작, 데이터 제어를 하기 위해 사용하는 언어이다. SQL 문장은 단순 스크립트가 아니라 이름에도 포함되어 있듯이, 일반적인 개발 언어처럼 독립된 하나의 개발 언어이다.

![](http://www.dbguide.net/publishing/img/knowledge/SQL_148.jpg)

> 공식적으로는 4가지 그룹으로 나눌 것을 권고한다.



#### TABLE

만약 아래 이미지의 왼쪽 처럼 정리되지 않은 자료가 있다면 어떨까?

![](http://www.dbguide.net/publishing/img/knowledge/SQL_149.jpg)

나도 알아보기 힘들고, 다른사람도 알아보기 힘들 것이다. 하지만 오른쪽은 한눈에 보기 쉬워 다른 사람에게도 도움이 될 수 있다. 

*여기서 테이블이란*

데이터들은 관계형 데이터베이스의 기본 단위인 테이블 형태로 저장된다. 테이블은 어느 특정 주제와 목적으로 만들어지는 일종의 집합이다. 

![](http://www.dbguide.net/publishing/img/knowledge/SQL_151.jpg)

위에 표로 얘기하자면, 맨위에 [선수, 팀, 연고지,,,등]이 각각의 칼럼이고, 해당 테이블은 반드시 하나의 칼럼을 가져야 한다.

![](http://www.dbguide.net/publishing/img/knowledge/SQL_152.jpg)

이청용 선수의 정보는 위와 같이 8개의 칼럼을 가지는 하나의 행으로 데이터화 되어 테이블에 저장된 것이다.



![](http://www.dbguide.net/publishing/img/knowledge/SQL_154.jpg)

![](http://www.dbguide.net/publishing/img/knowledge/SQL_155.jpg)

> 위의 테이블 구조에 대한 용어 설명이다.



그런데 여기서 **중요**한게 있다.

모든 데이터를 하나의 테이블로 저장하지 않는다는 것이다. 

![](http://www.dbguide.net/publishing/img/knowledge/SQL_156.jpg)

위와 같은 과정으로 테이블을 나누는 것을 정규화라고 한다. 이상현상 방지를 위한 중요한 프로세스중 하나이다.

여기서 눈여겨 볼 점은 구단테이블의 기본키가 선수테이블의 외부키로 들어와있다는 것이다. 

외부키(Foreign Key)는 `구단코드`와 같이 다른 테이블의 기본 키로 사용되면서 테이블과의 관계를 연결하는 역할을 하는 칼럼을 말한다.



#### ERD(Entity Relationship Diagram)

이거는 말하자면, 테이블끼리 관계를 표시한것이다. 다이어그램으로 그려져있어 한눈에 보기 쉽다(고 말한다.)

ERD의 구성 요소는 엔터티(Entity), 관계(Relationship), 속성(Attribute) 3가지이며 현실 세계의 데이터는 이 3가지 구성 요소로 모두 표현이 가능하다.

![](http://www.dbguide.net/publishing/img/knowledge/SQL_158.jpg)





## DDL (Data Definition Language)

> 데이터 정의어 라고 하며, 데이터의 구조를 만드는데 사용하는 언어들의 집합이다.



#### (1) 데이터의 유형

데이터베이스의 테이블 안에 특정 자료를 입력할 때, 그 공간에 넣는 자료의 유형이다. 그러니까, 특정 칼럼을 만드려고 할 때 이 칼럼이 받아들일 자료의 유형을 규정하는 것이다. 규정된 유형이 아닌 이 외의 유형의 데이터가 들어오면 에러가 발생된다.

유형과 함께 `크기(size)` 도 지정하는데 이것도 정말 중요하다.

![](http://www.dbguide.net/publishing/img/knowledge/SQL_162.jpg)

> 데이터의 유형 정리 이미지 이다.

문자열 유형을 테이블 안에 넣고 싶다면, CHAR 유형과 VARCHAR 유형(위 이미지에서 1,2번 행) 중 선택해야 하는 문제가 있다. `저장영역`과 문자열의 `비교방법`으로 구분할 수 있다.

**VARCHAR 유형**은 가변 길이이므로 필요한 영역은 실제 데이터 크기이며, 정의된 길이와 실제 데이터의 길이에 차이가 있는 칼럼에 적합하다.(팀명, 집 주소 등) VARCHAR에서 `(s)` 는 들어올 수 있는 데이터의 한계값을 의미한다.

비교 방법의 차이로, CHAR는 문자열을 비교할 때 `공백` 을 채워서 비교한다. 그에 반해 VARCHAR 유형에서는 맨 처음부터 한 문자씩 비교하고 공백도 하나의 문자로 취급하므로 끝의 **공백이 다르면 다른 문자로 판단**한다.

```sql
# CHAR 유형
'AA' = 'AA '
============================================================================
# VARCHAR 유형
'AA' != 'AA '
```



#### (2) CREATE TABLE

테이블은 일정한 형식에 의해 생성된다. 테이블을 생성하려면 여기에 입력 될 **데이터를 정의**하고, **데이터 유형**을 정해야한다.



1. 칼럼 정의

   *첫 번째로*, 모든 데이터를 고유하게 식별하게 해줄 수 있으면서, 반드시 값이 있는 단일 칼럼/조합 중에서 하나를 선정해서 **기본키(PK)로 지정**한다.(예: 학번, 선수ID 등)

   *그 다음으로*, 테이블과 테이블관의 관계를 정의하고 이상현상이 발생하지 않게 모델링과 정규화 과정을 거친다.

2. 테이블 CREATE 하기

   ```sql
   CREATE　TABLE　테이블이름 ( 칼럼명1 DATATYPE [DEFAULT 형식], 
                  			  칼럼명2 DATATYPE [DEFAULT 형식], 
                             칼럼명2 DATATYPE [DEFAULT 형식]) ;
   ```

   - 생성 시 주의할 규칙

     - 테이블 명은 객체를 의미할 수 있는 적절한 이름 사용(가능한 단수형)

     - 테이블 끼리의 이름은 중복되지 않게

     - 한 테이블 내에서 칼럼명 또한 중복되지 않게(다른 테이블 내 칼럼명과는 중복 가능)

     - 위 코드블럭 처럼 `테이블 이름`을 지정하고 각 칼럼들은 괄호 `"( )"` 로 묶어 지정

     -  `A-Z`, `a-z`, `0-9`, `_`, `$`, `#` 문자만 허용된다.

       ![](http://www.dbguide.net/publishing/img/knowledge/SQL_165.jpg)

       > 테이블 명의 잘못된 사례

3. 제약조건(CONSTRAINT)

   사용자가 원하는 조건의 데이터만 유지하기 위한 보편적인 방법으로 테이블의 특정 칼럼에 설정하는 제약. 

   이건 반드시 기술할 필요는 없지만, 이후에 *ALTER TABLE을 이용해서 추가, 수정하는 경우 데이터가 이미 입력된 경우라면* 처리 과정이 쉽지 않으므로 초기 테이블 생성 시점부터 적합한 제약 조건에 대한 충분한 검토가 있어야 한다.

   ![](http://www.dbguide.net/publishing/img/knowledge/SQL_166.jpg)

   - NULL

     NULL(ASCII 코드 00번)은 `공백`(BLANK, ASCII 코드 32번)이나 `숫자 0`(ZERO, ASCII 48)과는 전혀 다른 값이며,  **‘아직 정의되지 않은 미지의 값’**이거나 **‘현재 데이터를 입력하지 못하는 경우’**를 의미한다.

   - DEFAULT

     **기본값**이라고 하는DEFAULT는 사전에 설정할 수 있다. 데이터를 입력할 때 어떤 값을 명시해두지 않으면 `NULL` 이 입력되고, 디폴트 값을 정의했으면 `NULL`값 대신 정의된 기본값이 자동 입력 되어있다.

4. 생성된 테이블 구조 확인

   테이블 생성 후에 제대로 만들어졌는지 확인볼 필요 있다. 현재 내가 사용하는 Oracle 기반에서는 아래와 같은 명령어로 사용할 수있다.

   ```sql
   # Oracle SQL에서는
   DESCRIBE [테이블명]
   
   #또는 간략하게
   DESC [테이블명]
   ```

   > SQL Server에선  “sp_help ‘dbo.테이블명’”으로 해당 테이블에 대한 정보를 확인할 수 있다.

5. SELECT 문장을 통한 테이블 생성 사례

   DML 문장 중에서도 `SELECT`문을 사용해서 테이블을 생성하는 방법이 있다.  여기에서는 이걸 **CTAS 방법**(Create Table ~ As Select ~)이라고 한다.

   ```sql
   # Oracle SQL에서
   CREATE TABLE TEAM_TEMP AS SELECT * FROM TEAM; 
   ```

   ```SQL
   # 실행결과
   Oracle DESC TEAM_TEMP; 칼럼 NULL 가능 데이터 유형
   -------------- -------- -----------
   TEAM_ID NOT NULL CHAR(3) REGION_NAME NOT NULL VARCHAR2(4) TEAM_NAME NOT NULL VARCHAR2(40) E_TEAM_NAME VARCHAR2(50) ORIG_YYYY CHAR(4) STADIUM_ID NOT NULL CHAR(3) ZIP_CODE1 CHAR(3) ZIP_CODE2 CHAR(3) ADDRESS VARCHAR2(80) DDD VARCHAR2(3) TEL VARCHAR2(10) FAX VARCHAR2(10) HOMEPAGE VARCHAR2(50) OWNER VARCHAR2(10)
   ```

   이 방법은 칼럼 별로 데이터 유형을 재정의 하지 않아도 되는 장점이 있지만, 제약조건을 추가 하기 위해서는 `ALTER TABLE`기능을 사용해야 한다.



#### (3) ALTER TABLE

한 번 생성된 테이블은 사용자가 구조를 변경하기 전까지 생성 당시 구조를 유지한다. 하지만 업무나 시스템을 운영하다보면 변경해야 할 일이 있을 수 있다. 

이 때는 주로 **칼럼 추가/삭제** 또는 **제약조건 추가/삭제** 를 진행한다.

> 예시 코드 파일 참고





## DML (Data Manipulation Language)

> DDL 이 테이블의 구조를 만드는 명령어 라면, DML 이라는 데이터 조작어는 들어온 데이터들을 입력, 수정, 삭제, 조회하는 명령어의 집합이다.



1. *INSERT*
2. *UPDATE*
3. *DELETE*
4. *SELECT*



### INSERT

`INSERT` 명령어를 통해서 테이블에 데이터를 입력하는 방법은 두가지 유형이 있으며 한 번에 한건만 입력된다. 

```SQL
# 1번 방법
INSERT INTO 테이블명 (COLUMN_LIST)VALUES (COLUMN_LIST에 넣을 VALUE_LIST); 
# 2번 방법
INSERT INTO 테이블명VALUES (전체 COLUMN에 넣을 VALUE_LIST);
```

- 1번 방법: 

  이 방법은 테이블의 칼럼을 정의할 수 있다. 정의하지 않은 칼럼은 Default로 NULL 값이 입력된다.

- 2번 방법:

  모든 칼럼에 데이터를 입력하는 경우로 굳이 COLUMN_LIST를 언급하지 않아도 되지만, 칼럼의 순서대로 빠짐없이 데이터가 입력되어야 한다.

- 위의 2개 방법을 예시로 보면

  ```sql
  # 테이블명 : PLAYER 
  INSERT INTO PLAYER (PLAYER_ID, PLAYER_NAME, TEAM_ID, POSITION, HEIGHT,                         WEIGHT, BACK_NO) 
  VALUES ('2002007', '박지성', 'K07', 'MF', 178, 73, 7); 
  ```

  > 선수 테이블에, 박지성 선수의 데이터를 일부 칼럼만 입력했을 때의 예시이다. 위의 명령어를 통해 1개의 행이 만들어졌다.

  ![](http://www.dbguide.net/publishing/img/knowledge/SQL_167.jpg)

  > 위 명령어의 결과이다.

  2번 방법으로 입력해보면,

  ```sql
  INSERT INTO PLAYER VALUES ('2002010','이청용','K07','','BlueDragon',
                             '2002','MF','17',NULL, NULL,'1',180,69);
  ```

  ![](http://www.dbguide.net/publishing/img/knowledge/SQL_168.jpg)

  > 데이터를 입력하는 경우 정의되지 않은 미지의 값은 `' '`  따옴표를 붙여서 표현하거나 `null` 로 표현할 수 있다.

  1번과 2번 방법의 차이를 요약하자면, 1번은 칼럼 명을 지정해서 필요한 값만 입력하는 방법이며 2번 은 모든 칼럼에 순서대로 입력하는 것이다.

  

### UPDATE

입력한 데이터 중 일부를 수정하고 싶을 때 사용한다. 방법은 다음과 같다.

```SQL
UPDATE 테이블명 SET 수정되어야 할 칼럼명 = 수정되기를 원하는 새로운 값;
```

아래의 두 개 예제를 통해 연습해보자

```SQL
# 예제 1 (선수 테이블의 백넘버를 일괄적으로 99로 모두 수정)
UPDATE PLAYER SET BACK_NO = 99; 

# 예제 2 (선수 테이블의 포지션을 미드필더로 모두 수정)
UPDATE PLAYER SET POSITION = 'MF'; 
```



### DELETE

테이블의 정보가 필요없게 될 때 삭제를 수행한다. 이 명령어를 사용할 때, FROM 문구는 생략이 가능한 키워드 이다. 만약, `WHERE` 절을 사용하지 않는다면 테이블 전체가 삭제된다.

```SQL
DELETE [FROM] 삭제를 원하는 정보가 들어있는 테이블명;
```

아래 예제로 봤을 때

```SQL
# 예제(선수 테이블의 데이터 전부를 삭제)
DELETE FROM PLAYER;
```

- 참고사항:

   DDL(CREATE, ALTER, RENAME, DROP) 명령어의 경우, 직접 테이블에 영향을 미치게 되어 입력 즉시 자동 커밋된다. 

  하지만 DML(INSERT, UPDATE, DELETE, SELECT) 명령어의 경우, 테이블을 메모리 버퍼에 올려놓고 작업하게 되어 실시간 반영되는 것은 아니다. 실제 테이블에 반영되려면 커밋 명령어를 입력해서 트랜잭션을 종료해야 한다.

  또한, 시스템 활용 측면에서, 삭제된 데이터를 로그로 저장하는 **DELETE TABLE** 보다는 시스템 부하가 적은 **TRUNCATE TABLE**을 권고한다.(*단 롤백 불가)



### SELECT

사용자가 입력한 데이터를 **조회**(열)할 때 시작하는 명령어다.

- 대표 예제 1(기본)

  ```SQL
  SELECT PLAYER_ID, PLAYER_NAME, TEAM_ID, POSITION, HEIGHT, WEIGHT, BACK_NO FROM PLAYER;
  ```

- 대표 예제 2(DISTINCT 옵션)

  ```SQL
  SELECT ALL POSITION FROM PLAYER;
   # ALL은 생략 가능한 키워드이므로 아래 SQL 문장도 같은 결과를 출력한다, 
  
  SELECT POSITION FROM PLAYER;
  ```

  ```sql
  SELECT DISTINCT POSITION FROM PLAYER;
  ```

  > DISTINCT의 실행결과를 보면 480개의 모든 행이 출력된 것이 아니라, 포지션의 종류인 4개의 행과 `NULL`데이터의 총 5건만 출력된다.

- 대표 예제 3(WILDCARD 사용하기)

  모든 칼럼 정보를 보고 싶을 경우에는 와일드카드로 `애스터리스크(*)`를 사용하여 조회할 수 있다.

  ```sql
  SELECT * FROM 테이블명;
  ```

- 대표 예제 4(ALIAS(별명) 부여하기)

  조회된 결과에 일종의 별명(ALIAS, ALIASES)을 부여해서 칼럼 레이블을 변경할 수 있다.  칼럼명 뒤에 바로 붙일 수 있으며, `AS` 라는 키워드를 붙여서 사용할 수도 있다.

  ```sql
  # 예제 1
  SELECT PLAYER_NAME AS 선수명, POSITION AS 위치, 
         HEIGHT AS 키, WEIGHT AS 몸무게 
  FROM PLAYER; 
  
  # 예제 2
  SELECT PLAYER_NAME 선수명, POSITION 위치,
         HEIGHT 키, WEIGHT 몸무게 
  FROM PLAYER;
  ```

  > 칼럼 별명에서 AS를 꼭 사용하지 않아도 되므로, 두 예제는 같은 결과를 보여준다.

- 칼럼 별명 부여할 때 주의사항

  별명 중간에 공백이 들어가는 경우 `" "` 를 사용해야 한다.

  ```SQL
  SELECT PLAYER_NAME "선수 이름", POSITION "그라운드 포지션", 
         HEIGHT "키", WEIGHT "몸무게" 
  FROM PLAYER;
  ```



### 산술 연산자와 합성 연산자

**산술 연산자**는 숫자(NUMBER)와 시간(DATE) 자료형에 대해 적용된다.

![](http://www.dbguide.net/publishing/img/knowledge/SQL_169.jpg)

```SQL
# 산술연산 예제
SELECT PLAYER_NAME 이름, HEIGHT - WEIGHT "키-몸무게" 
FROM PLAYER;
```

> 마이너스 연산한 새로운 칼럼의 별명까지 부여했다.

**합성 연산자**는 문자와 문자를 연결한다. 

- 특징:

  - 문자와 문자를 연결할 때 <2개의 수직 바`||`> 를 통해 이루어 진다.(SQL server에서는 `+`)
  -  CONCAT (string1, string2) 함수를 사용할 수 있다.

- 예제: 선수명 선수, 키 cm, 몸무게 kg `예) 박지성 선수, 176 cm, 70 kg`

  ```sql
  SELECT PLAYER_NAME || '선수,' || HEIGHT || 'cm,' || WEIGHT || 'kg' 체격정보 FROM PLAYER;
  ```

  

