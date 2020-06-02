## 모델링과 성능

- 데이터 모델링 유의사항 :
  - 중복 : 데이터가 여러 장소에 같은 정보를 저장하는 잘못을 하지 않도록 유의
  - 비유연성 : 사소한 업무변화에도 많은 유지보수 소요가 들 수 있다.  그렇기 때문에 `데이터 정의` 와 `프로세스` 를 분리해서 비용요소 줄인다.
  - 비일관성 : 모델링을 할 때 데이터간의 상호 연관관계에 대해 명확하게 정의해서 비일관성을 줄인다.
- 엔터티의 특징 :
  - 해당 업무에서 필요한 정보
  - 유일한 식별자에 의한 식별 가능
  - 영속적으로 존재하는 두 개 이상의 인스턴스 집합
  - 반드시 속성이 있어야함
  - 한개의 속성은 한개의 속성값을 가진다.
- 데이터 베이스의 스키마구조 3단계 :
  - 외부스키마 - 개념스키마 - 내부스키마
- 속성을 특성에 따라 분류 :
  - 기본속성 - 설계속성 - 파생속성
- 도메인
- 속성의 명칭 부여 방법 :
  - 약어 사용 X, 복합명사를 사용해서 구체적으로 명명
  - 속성에는 서술식 용어 사용 X
  - 가급적 해당 없무에서 사용하는 일므
- 관계 정의 시 체크항목 :
  - 두 개의 엔터티 사이에 연관규칙 O
  - 정보의 조합 O
  - 관계 연결 규칙 O
  - 관계 연결하는 동사 O
- 식별자 - 비식별자 관계 :
  - 관계 걍약에서 연관성이 약하면 비식별자 관계를 고려
  - 자식 테이블에서 독립적인 주키를 가지고자 하면 비식별자 관계 고려
  - 모든 관계가 식별자 관계가 되면, SQL 쿼리의 복잡성이 증가한다.
  - 부모 - 손자 까지 흘리고 싶으면 식별자관계로 연결
- 식별자 분류체계
  - 이미지 삽입
- 정규화
  - 1차 정규화 : 중복제거(행 증가 / 한 행에 여러 데이터 저장된것을 각 행으로 / 칼럼단위의 중복도 1차 정규화 대상)
  - 2차 정규화 : 부분 함수종속성 에 따른 분할
  - 3차 정규화 : 기본 키 이외 중복 없는지 
- 반정규화 판단요소
  - 대량 데이터 탐색 시, 인덱스가 아니라 파티셔닝 같은 물리저장 기법 활용해야함
  - 반정규화 테이블은 다양한 유형에 적용 가능
  - 이전,이후 위치 레코드에 대한 탐색은 윈도우 함수로 사용
- 한 테이블에 칼럼이 많을 때
  - 디스크 I/O가 발생할 수 있는 가능성이 커진다
  - 상대적으로 접근 빈도가 낮은 칼럼을 1:1 테이블 분리하면 좋다
  - NULL 값이 많아 뒤로 빼는 것도 좋은데 이렇게 하면 나중에 값이 더 추가되면 `로우체인`이 발생된다.
- 여러 테이블을 함께 조회하는 경우가 많을 때
  - UNION/UNION ALL 은 개별조회에 따른 성능저하가 있다.
  - 데이터 테이블을 통합하고 PK체계나 일반속성으로 각 사건 구분하는게 좋다.



## SQL 기본

- 비절차적 데이터 조작어DML

  - 사용자가 무슨 데이터를 원하는 지만 명세함
  - 절차적 데이터는 데이터를 어떻게 접근해야 하는지 명세함(PL/SQL)

- PK 제약조건 생성방법

  ```sql
  ALTER TABLE product
  ADD CONSTRAINT product_pk PRIMARY KEY (prod_id) ;
  
  # 또는
  
  CREATE TABLE product
  		(...,
           CONSTRAINT product_pk PRIMARY KEY (prod_id)
          ) ;
  ```

- SQL server 에서 데이터 타입 변경할 때

  - SQL server 는 칼럼을 동시에 수정하는 구문은 지원 X
  - SQL server 는 괄호 사용 X

- 제한사항 규정 (참조무결성 규정 관련)

  1. Delete Action : `cascade`, `set null`, `set default`, `restrict`
     - `Cascade` : Master 삭제시 Child 같이 삭제
     - `Set Null` : Master 삭제시 Child의 해당 필드를 NULL
     - `Set Default` : Master 삭제시 Child의 해당 필드 디폴트 값으로 설정
     - `Restrict` : Child 테이블에 PK 값이 없는 경우만 Master 삭제 허용
     - `No Action` : 참조무결성을 위반하는 삭제/수정 액션을 취하지 않는다.
  2. Insert Action : `Automatic`, `set null`, `set default`, `dependent`
     - `Automatic` : Master 테이블에 PK가 없을 때 Master PK를 생성 후 Child 입력
     - `Set Null` : Master 테이블에 PK가 없을 때 Child의 FK를 NULL 값으로
     - `Set Default` : Master 테이블에 PK가 없을 때 Child의 FK를 지정된 기본값으로 입력
     - `dependent` : Master 테이블에 PK가 존재할 때만 Child 입력 허용
     - `No Action` : 참조무결성을 위반하는 입력 액션을 취하지 않는다.

- SQL 문장 볼 시 NOT NULL 조건 및 PK 입력 중복 했는지 확인

- 칼럼에 PK가 있다는 것은 NULL이 없다는 것 `COUNT(*)` 와 `COUNT(칼럼1)`가 같다.

- 외부키 FK 관련 조건 

  - 테이블 생성 시 설정할 수 있다.
  - FK는 널 값을 가질 수 있다.
  - 한 테이블에 여러개 존재할 수 있다.
  - FK는 참조 무결성 제약을 받을 수 있다.

- 칼럼 삭제

  ```SQL
  ALTER TABLE 테이블명
  DROP COLUMN 삭제할칼럼
  ;
  ```

- 칼럼 명 변경

  ```sql
  RENAME 테이블명 TO 새로운테이블명 ;
  ```

- 제한사항 관련 유의할 예제

  ```sql
  ALTER TABLE 주문
  ADD CONSTRAINT fk_001 FOREIGN KEY (고객ID)
  REFERENCES 고객 (고객ID) 
  ON DELETE SET NULL
  ;
  ```

  > '주문' 테이블의 FK로 고객 id를 '고객' 테이블에서 참조하는데, SET NULL 이라는 제약조건을 걸었다.

- 테이블이 필요없다 판단되어 데이터를 삭제할 때 좋은 방법

  ```SQL
  DELETE FROM 테이블명 ;
  ```

- DELETE, TRUNCATE, DROP 명령어의 상호 비교

  <img src="C:\Users\user\Desktop\KakaoTalk_20200528_162834419.jpg" alt="KakaoTalk_20200528_162834419" style="zoom:80%;" />

- NULL 값을 조건절에서 사용하는 경우, `IS NULL`, `IS NOT NULL`을 사용해야한다.

  ```sql
  @ 예시
  SELECT *
  FROM 테이블명
  WHERE 칼럼1 IS NOT NULL ;
  ```

- 아래와 같은 테이블 있고, DDL 문장에서 SQL 수행할 때 유의 점

  |  서비스 번호   |     서비스 명     |   개시일자    |
  | :------------: | :---------------: | :-----------: |
  | VARCHAR(10) PK | VARCHAR(100) NULL | DATE NOT NULL |

  - 사례 1

    ```sql
    INSERT INTO 서비스
    VALUES ('999', '', '2015-11-11') ;
    ```

    > 위와 같이 입력하면 서비스명의 값은 오라클에서는 NULL로 인식된다.

  - 사례 2

    ```SQL
    SELECT *
    FROM 서비스
    WHERE 서비스명='' ;
    ```

    > 오라클에서 데이터를 조회하려면 '서비스명 IS NULL'로 조회해야 한다

  - 사례 3

    ```SQL
    SELECT *
    FROM 서비스
    WHERE 서비스명 IS NULL ;
    ```

    > SQL server 에서 조회하려면 '서비스명='' ' 으로 조회 해야 한다.

- 날짜표현

  - 1/24/60 == 1분
  - 1/24/(60/10) == 10분

- IF - THEN - ELSE 의 다르지만 같은 표현

  ```sql
  @ 1번
  SELECT loc
  	CASE WHEN loc='new york' THEN 'east'
  	ELSE 'etc'
  	END as AREA
  FROM dept ;
  
  @ 2번
  SELECT loc
  	CASE loc WHEN'new york' THEN 'east'
  	ELSE 'etc'
  	END as AREA
  FROM dept ;
  ```

- 단일행 함수에서 NULL 관련 함수

  - NVL / ISNULL

    ```sql
    NVL(1, 2)
    ```

    > '1'의 결과값이 NULL 이면 지정된 값('2')을 반환한다. 

  - NULLIF

    ```SQL
    NULLIF(1, 2)
    ```

    > '1'의 결과값이 '2' 와 같으면 NULL 반환. 같지 않으면 '1' 다시 

  - COALESCE

    ```SQL
    COALESCE(C1, C2, C3, ...)
    ```

    > 각각의 표현식에서 NULL이 아닌 최초의 표현식을 리턴한다.

- NULL 이 있는 데이터 테이블에서 GROUP BY 절을 통해서 그룹화 하면 NULL 을 가진 데이터들 끼리도 하나의 그룹화 된다.

- 결과 값이 하나로 된 칼럼은 AVG 등의 그룹함수 사용 못한다.

  - 예시 : AVG(COUNT(*))

- 실행 순서에 근거해서, SELECT 절에 기술 되지 않은 칼럼을 가지고 ORDER BY에서 정렬할 수 없다.

- ORDER BY 절에서 칼럼명 대신 별칭(AS)이나 칼럼 순서를 나타내는 정수도 사용가능하며, 혼용 가능하다.

- 문장의 실행 순서

  1. FROM
  2. WHERE
  3. GROUP BY
  4. HAVING
  5. SELECT
  6. ORDER BY

- JOIN의 특성에 대해

  - 일반적으로 조인은 PK 와 FK 값의 연관성에 의해 성립된다
  - DBMS 옵티마이저는 FROM절에 나열된 테이블이 많아도 항상 2개씩 짝을 지어 조인을 수행한다
  - 대부분 비동등조인을 수행할 수 있지만, 때로는 설계상의 이유로 불가능할 때도 있다
  - 동등 조인은 `=` 연산자에 의해서만 가능하고, 이 외의 연산자들은 모두 비동등 조인이다

