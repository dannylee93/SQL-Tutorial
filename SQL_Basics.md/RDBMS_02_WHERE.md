# 관계형 데이터베이스

수 많은 사용자들에게 효율적인 데이터 관리와 예상치 못한 사건으로 인한 데이터 손상을 막고, 필요하다면 데이터 복구까지 할 수 있는 이런 요구사항을 만족시켜주는 것이 DBMS(Database Management System) 라고 한다.



#### 관계형 데이터베이스(Relational Database)

만약 하나의 파일을 여러 지사에서 복사해 나눠가진 다음, 변경작업을 하면 이 여러 개의 변경된 파일은 결국 데이터의 불 일치성이 생긴다. 그래서 관계형 데이터베이스는 **정규화를 통해 합리적인 테이블 모델링으로 이상현상을 제거하고 많은 사용자들이 동시에 데이터를 공유 또는 조작할 수** 있게 구현 해준다.



## WHERE 조건절

`WHERE` 는 행의 특정 데이터 값을 조회하거나 비교하여 연산처리할 때 사용하는 명령어 이며,  명령어 뒤에 조건을 부여하여 특정 데이터를 조회한다.

`FROM`이라는 테이블 지정하는 명령어 다음에 붙여 사용한다.



### 주요 특징

- 조회 하려는 행의 조건들을 지정할 수 있다.

- FROM 다음에 위치

- 각종 연산자와 열 이름, 표현식, 숫자, 문자 등을 활용해서 조건 부여 가능하다.

  | 연산자 종류 |       설명       |      예시       |
  | :---------: | :--------------: | :-------------: |
  | 비교 연산자 |   조건을 비교    |  `=`, `<`, `>`  |
  | SQL 연산자  | 조건 비교를 확장 | `BETWEEN`, `IN` |
  | 논리 연산자 | 조건 논리를 연결 |   `AND`, `OR`   |

  - 우선순위: **(중요)**

    - 괄호 > 부정 연산 > 비교 연산 > SQL 연산 순으로 처리된다.

    - 논리 연산자는 NOT - AND - OR 순으로 처리된다.

      ![](http://www.dbguide.net/publishing/img/knowledge/SQL_173.jpg)

      

- 연산자 종류

  ![](http://www.dbguide.net/publishing/img/knowledge/SQL_172.jpg)



### 1. 비교 연산자

- 등호연산자

  ```sql
  # 예제 1(숫자 조건을 통해서 조회했다.)
  SELECT *
  FROM employees
  WHERE employee_id = 100;
  
  # 예제 2(문자열 조건을 통해서 조회했다.)
  SELECT *
  FROM employees
  WHERE employee_id = 'David';
  ```

- 부등호 연산자

  ```sql
  # 예제(id번호가 105 이상인 직원 출력)
  SELECT *
  FROM employees
  WHERE employee_id >= 105;
  ```

- 주의할 점(문자 유형 비교 방법)

  ![](http://www.dbguide.net/publishing/img/knowledge/SQL_175.jpg)

### 2. SQL 연산자

SQL연산자는 비교연산자 보다 조금더 확장된 연산자로, 조회조건을 확장할 때 쓴다.

- BETWEEN a AND b `이상/이하`

  ```sql
  # 예제(salary가 10000이상 20000이하인 직원 정보 조회)
  SELECT *
  FROM employees
  WHERE salary BETWEEN 10000 AND 20000;
  ```

- IN

  조회 하고 싶은 데이터가 `여러 개` 일 때 사용한다. (참고 :`=`연산자는 하나만 조회할 수 있음)

  ```sql
  # 예제(salary가 10000, 17000, 24000인 직원 조회)
  SELECT *
  FROM employees
  WHERE salary IN (10000, 17000, 24000);
  ```

- LIKE

  조회할 때 조건이 명확하지 않을 때 사용한다.

  - `%`, `_` 같은 기호 연산자(wild-card)랑 함께 사용한다.
  - 조건 안에 문자나 숫자 포함할 수 있다.
  - `%` = 모든 문자 / `_` = 한 글자

  ```sql
  # 예제(job_id가 `AD`로 시작하는 모든 데이터)
  SELECT *
  FROM employees
  WHERE job_id LIKE 'AD%';
  
  # 변형: '%AD%' , '%AD' 
  ```

  ```SQL
  # 예제(job_id가 `AD`로 시작하고, 뒤에 3글자 오는 데이터)
  SELECT *
  FROM employees
  WHERE job_id LIKE 'AD_ _ _'; (보기 편하게 글자를 띄웠다.)
  ```

- IS NULL

  데이터 값이 `NULL` 인 데이터를 조회한다.

  ```sql
  # 예제(manager_id가 null 인 데이터)
  SELECT *
  FROM employees
  WHERE manager_id IS NULL;
  ```



### 3. 논리 연산자

여러 조건을 논리적으로 계속 추가하면서 연결할 때 사용하는 연산자 이다.

- AND (교집합)

  ```sql
  # 예제(2개의 조건을 모두 만족하는 행 데이터)
  SELECT *
  FROM employees
  WHERE salary > 4000
  AND job_id = 'IT_PROG';
  ```

- OR (합집합)

  ```sql
  # 예제(각 조건을 하나라도 만족하는 행 데이터)
  SELECT *
  FROM employees
  WHERE job_id = 'IT_PROG'
  OR job_id = 'FI_ACCOUNT';
  ```

- NOT

  `중요!` NOT연산자는 조건을 부정으로 만드는 역할을 하기 때문에 부정 연산자라고 하며 비교연산자 또는 SQL연산자로 사용할 수 있다.

  ```sql
  # 예제(NOT을 비교연산자를 통해 구현)
  SELECT *
  FROM employees
  WHERE employee_id <> 105;
  
  # 예제(NOT을 SQL연산자를 통해 구현)
  SELECT *
  FROM employees
  WHERE manager_id IS NOT NULL;
  ```

  