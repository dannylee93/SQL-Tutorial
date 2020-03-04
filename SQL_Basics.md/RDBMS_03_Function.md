# 관계형 데이터베이스

수 많은 사용자들에게 효율적인 데이터 관리와 예상치 못한 사건으로 인한 데이터 손상을 막고, 필요하다면 데이터 복구까지 할 수 있는 이런 요구사항을 만족시켜주는 것이 DBMS(Database Management System) 라고 한다.



#### 관계형 데이터베이스(Relational Database)

만약 하나의 파일을 여러 지사에서 복사해 나눠가진 다음, 변경작업을 하면 이 여러 개의 변경된 파일은 결국 데이터의 불 일치성이 생긴다. 그래서 관계형 데이터베이스는 **정규화를 통해 합리적인 테이블 모델링으로 이상현상을 제거하고 많은 사용자들이 동시에 데이터를 공유 또는 조작할 수** 있게 구현 해준다.



## Function

> 함수란 사용자가 입력한 값 `X` 를 넣으면 정해 놓은 출력 값 `Y`가 나오는 개념이다.

DB guide 에서는 내장함수와 사용자 정의 함수로 나누고, 그 내장함수에서 단일행 함수와 다중행 함수로 나눈다. **단일행 함수**는 함수의 입력값이 단일행 값인 함수이며, **다중행 함수**는 `집계함수, 그룹함수, 윈도우 함수`로 또 나눌 수 있다.

#### 사용하는 목적

1. `단일 행 함수` : 데이터의 값을 계산하거나 조작

2. `그룹 함수` : 행의 그룹에 대해 계산하거나 요약

3. 열의 **데이터 타입** 변경.

4. **중요 포인트**: 

   단일 행 함수는 한개 열로 하나의 열을 뽑아내고, 그룹 함수는 여러 열로 하나의 새로운 행을 만든다.

#### 단일 행 함수의 목적

![](http://www.dbguide.net/publishing/img/knowledge/SQL_181.jpg)



#### 주요 특징

- 각 행에 대해 수행한다.
- 데이터 타입에 맞는 함수를 사용해야한다.
- 행 별로 하나의 결과를 반환한다.
- SELECT, WHERE, ORDER BY 절에 사용 가능하다.
- 함수 속 함수처럼 중첩해서 사용할 수 있다.
- 중첩 시, 가장 안쪽 단계에서 바깥쪽 단계 순으로 진행한다.



### 1. 문자 타입 함수

문자 타입 함수는 주로 데이터 조작에 쓰이며 종류는 아래와 같고, 문자나 문자열 데이터는 `''` 작은 따옴표를 붙여서 문자타입으로 표현한다.

1. 종류

   ![](http://www.dbguide.net/publishing/img/knowledge/SQL_182.jpg)

2. 문자 타입 함수 실전 코드

   ![](http://www.dbguide.net/publishing/img/knowledge/SQL_183.jpg)

   - **LOWER, UPPER, INICAP** 데이터 값을 대소문자로 변환하기

     ```SQL
     # 예제(대소문자, 첫문자 대문자 적용해보기)
     SELECT last_name,
     	   LOWER(last_name) LOWER 적용,
     	   UPPER(last_name) UPPER 적용,
     	   email,
     	   INICAP(email) INICAP 적용
     FROM employees;
     ```

   - **SUBSTAR** 지정한 길이만큼 문자열 추출하기

     ```SQL
     # 예제( SUBSTR('문자열 또는 열 이름', 시작 위치, 추출할 길이))
     SELECT job_id,
     	   SUBSTR(job_id, 1, 2) AS 적용결과
     FROM employees;
     ```

   - **REPLACE** 특정 문자를 찾아 바꾸기

     ```sql
     # 예제( REPLACE('문자열 또는 열 이름', 바꾸려는 문자열, 바뀐 문자열))
     SELECT job_id,
     	   REPLACE(job_id, 'ACCOUNT', 'CHANGED') AS 적용결과
     FROM employees;
     ```

   - **LPAD, RPAD** 특정 문자로 자릿수 채우기(== Left-Padding, Right-Padding)

     ```sql
     # 예제( LPAD('문자열 또는 열 이름', 만들어질 자릿수(숫자), 채워질 문자/숫자))
     SELECT first_name,
     	   LPAD(first_name, 12, '*') AS 적용결과
     FROM employees;
     ```

   - **LTRIM, RTRIM** 특정 문자 삭제하기

     LTRIM은 왼쪽부터 지정한 문자를 지우고 RTRIM은 오른쪽부터 지우는 함수다. 1개만 삭제하고 끝이 난다.

     ```SQL
     # 예제( LTRIM('문자열 또는 열 이름', 삭제할 문자))
     SELECT job_id,
     	   LTRIM(job_id, 'F') AS 적용결과1,
     	   LTRIM(job_id, 'T') AS 적용결과2
     FROM employees;
     ```

   - **TRIM** 공백 제거하기

     아래 예제는 dual 테이블에서 문자열 시작하는 양쪽과 `-` 안쪽에 한칸씩 양옆을 띄웠다. 

     아래 예제의 실행결과는 `start- space -end` 로 문자열 안에 있는 공백은 없어지지 않는 것을 알 수 있다.

     ```sql
     # 예제( TRIM('문자열 또는 열 이름'))
     SELECT 'start'||TRIM('  - space -  ') ||'end'
     FROM dual;
     
     # 해석: 
     ```

     > dual은 임의의 값을 알고자 하거나 특정 테이블을 참고하지 않아도 될 때 사용하는 하나의 'X' 값을 가진 테이블이다.



### 2. 숫자 타입 함수

숫자 타입 함수는 주로 숫자를 계산하거나 계산이 끝난 후에 추가로 가공 처리를 할 때 사용한다. SQL은 다양한 숫자 타입 함수를 제공한다.(ROUND와 TRUNC를 제일 많이 사용한다고 한다.)

1. 종류와 사례

   ![](http://www.dbguide.net/publishing/img/knowledge/SQL_184.jpg)

   ![](http://www.dbguide.net/publishing/img/knowledge/SQL_185.jpg)

2. 숫자 타입 함수 실전 코드

   - ROUND 숫자 반올림

     반올림할 자리 값이 **양수** 이면 소수 자리에서 반올림하고, **음수** 이면 정수 자리에서 반올림 한다.

     <img src="https://thebook.io/img/006977/100.jpg" style="zoom:50%;" />

     ```SQL
     # 예제 (ROUND("숫자 또는 열 이름", 반올림할 자리 값))
     SELECT salary,
     	   salary/30 일급,
     	   ROUND(salary/30, 0) 적용결과0,
     	   ROUND(salary/30, 1) 적용결과1,
     	   ROUND(salary/30, -1) 적용결과M1,
     FROM employees;
     ```

   - TRUNC 숫자 절삭

     기본 문법은 ROUND와 비슷하며, **양수**는 소수자리 / **음수**는 정수자리에서 절삭한다.

     ```SQL
     # 예제 (TRUNC("숫자 또는 열 이름", 절삭할 자리 값))
     SELECT salary,
     	   salary/30 일급,
     	   TRUNC(salary/30, 0) 적용결과0,
     	   TRUNC(salary/30, 1) 적용결과1,
     	   TRUNC(salary/30, -1) 적용결과M1,
     FROM employees;
     ```

   - 이 외에도 다양한 숫자함수 위에 표에 예제 있지만 자주 사용하지는 않는다고 한다.



### 3. 날짜 타입 함수

데이터의 날짜를 계산하고 처리할 때 사용한다.  이 타입의 함수는 날짜를 연산해서 출력하는 `MONTHS_BETWEEN` 외에는 모두 결과를 날짜 타입으로 출력합니다.

1. 연산규칙

   - 날짜 `+` `-`숫자 == 날짜 결과

   - 날짜 `-`날짜 == 두 날짜의 일 수(숫자)

   - 날짜에 시간을 더할 때 : 날짜 + 숫자/24

     ![](http://www.dbguide.net/publishing/img/knowledge/SQL_187.jpg)

   - 예시`SYSDATE`

     ```sql
     SELECT TO_CAHR(SYSDATE, 'YY/MM/DD/HH24:MI') 오늘날짜,
     	   SYSDATE +1 AS 더하기1,
     	   SYSDATE -1 AS 빼기1,
     	   TO_DATE('20171202') - TO_DATE('20171201') 날짜빼기,
     	   SYSDATE +13/24 AS 시간더하기,
     FROM dual;
     ```

   - 결과:

     | 구분 |    오늘날짜    | 더하기1  |  빼기1   | 날짜빼기 | 시간더하기 |
     | :--: | :------------: | :------: | :------: | :------: | :--------: |
     | 내용 | 17/10/04 11:10 | 17/10/05 | 17/10/03 |    1     |  17/10/05  |

     

2. 날짜 타입 함수 종류

   ![](http://www.dbguide.net/publishing/img/knowledge/SQL_186.jpg)

3. 이 외에도 자주쓰는 종류

   - `ROUND`  날짜를 가장 가까운 연도 또는 월로 **반올림**

   - `TRUNC`  날짜를 가장 가까운 연도 또는 월로 **절삭**

     ```sql
     # ROUND/TRUNC 예제 ("연","월"을 각각 지정할 수 있다.)
     SELECT hire_date,
     	   ROUND(hire_date, 'MONTH') AS 적용결과1,
     	   ROUND(hire_date, 'YEAR') AS 적용결과2,
     	   TRUNC(hire_date, 'MONTH') AS 적용결과3,
     	   TRUNC(hire_date, 'YEAR') AS 적용결과4,
     FROM employees;
     ```

     > 10월 5일로 예를 들면, 날짜는 각각 11월 1일(ROUND) / 10월 1일(TRUNC)이 될 수 있다.

   - `MONTHS_BETWEEN`  두 날짜 **사이의 개월 수** 계산하기

     ```SQL
     # 패턴
     MONTHS_BETWEEN(날짜, 날짜)
     ```

   - `ADD_MONTHS`  월에 날짜 **더하기**

     ```sql
     # 패턴
     ADD_MONTHS(날짜, 숫자)
     ```

   - `NEXT_DAY` 돌아오는 요일 계산(일요일은 `1`, 금요일은 `6`)

     ```SQL
     # 패턴(문자열이나 숫자로 지정할 수 있다.)
     NEXT_DAY(날짜, "요일"또는숫자)
     ```

   - `LAST_DAY` 월의 마지막날로 계산

     ```SQL
     # 패턴
     LAST_DAY(날짜)
     ```



### 4. 변환 함수

SQL은 각 열의 데이터 타입을 정해 놓는다. 근데 이 타입을 변환해야 할 때도 있다. 이 때 사용하는 것이 변환 함수다. 시스템에 의해 **자동**(암시적) 으로 또는 **수동**(명시적)으로 실행된다.

![](http://www.dbguide.net/publishing/img/knowledge/SQL_188.jpg)

1. 자동 데이터 타입 변환(암시적)

   예를 들어, 문자열을 숫자로 나타낼 수 있는 경우에만 `VARCAHR2` 타입이 `NUMBER`타입으로 변환되고, 문자열은 데이터 베이스 시스템에 설정된 날짜 데이터 타입과 같은 경우에만 `VARCHAR2`타입이 `DATE` 타입으로 변환된다.(자동으로 변환은 되더라도 적당히 와꾸는 맞아야 된다는 말)

   ```SQL
   SELECT 1 + '2'
   FROM dual;
   ```

   > 자동 데이터 변환 사례 코드

   위의 예제코드는 `''`로 묶었기 때문에 문자열로 입력했지만 결과는 `3`으로 잘 출력되서 나온다.

   데이터베이스 시스템이 자동으로 변환 해줬기 때문이다. 다만 수동을 권장한다.

   <img src="https://thebook.io/img/006977/110.jpg" style="zoom:80%;" />

   

2. 수동 데이터 타입 변환(명시적) ~~* 명시한다는 말~~

   직접 변환하고 싶은 타입으로 변환해준다.

   ![](https://thebook.io/img/006977/111.jpg)

   - `TO_CHAR`  숫자, 문자, 날짜 값을 지정 형식의 **VARCHAR2 타입**으로 변환한다.

     ```sql
     # 패턴(다양한 [날짜,시간,기타,숫자]지정형식 p.112~115 참고하기)
     TO_CHAR(데이터 타입, '지정형식')
     ```

   - `TO_NUMBER`  **문자**(숫자타입의 문자열)를 **숫자** 타입으로

     ```sql
     # 패턴
     TO_NUMBER(number)
     ```

   - `TO_DATE`  날짜를 나타내는 **문자열**을 지정 형식의 **날짜 타입**으로

     ```sql
     # 패턴
     TO_DATE(문자열, '지정형식')
     ```



### 5. 일반 함수

DB-guide 에서는 NULL과 관련한 내용들만 추가로 제시되어있다. 공식 가이드에 따라 정리한다.

1. `NVL`  null값 처리하기
2. NULL과 공집합
3. `NULLIF`  표현식 1이 표현식 2와 같으면 NULL을, 같지 않으면 표현식 1을 리턴
4. `COALESCE` 표현식을 몇개 넣을지 한정하지 않으며, 임의의 표현식이 쭉 괄호`()`안에 나열 되었을 때, NULL 이 아닌 최초의 표현식을 리턴하고, 모든 표현식이 NULL이면 그냥 NULL을 리턴



#### NULL 관련 함수 종류

![](http://www.dbguide.net/publishing/img/knowledge/SQL_192.jpg)

1. NVL

   데이터 값이 `null`과 연산되면 결과가 `null` 이된다. 이럴 때 사용하는 것이 NVL

   ```sql
   # 패턴
   NVL(열 이름, 치환하고 싶은 값(null에서 변환하고자 하는 값))
   
   # 예시(salary와 commission_pct를 곱하되 null이 있는 행은 1로 출력)
   SELECT salary * NVL(commission_pct, 1)
   FROM employees;
   ```

   ```sql
   # 응용패턴(null이 아니면 2번열, null이면 3번열로 출력)
   NVL2(열 이름1, 열 이름2, 열 이름 3)
   ```

    

2. NULL과 공집합(원소가 없는 집합)

   일반적으로 NVL를 사용한다.

   - 공집합을 발생시키기 위해 테이블 내에 존재하지 않는 데이터를 검색해본다.
   - NVL함수를 이용해서 공집합을 9999로 임의 변경해본다
   - 적절한 집계함수(그룹함수)를 찾아 NVL함수 대신 적용
   - 집계 함수를 인수로 한 NVL/ISNULL 함수를 이용해서 공집합인 경우에도 빈칸이 아닌 9999로 출력하게 한다.
   - `결과`: 2번 순서로 9999까지 해봐도 없는 데이터기 때문에 `데이터를 찾을수 없다`고 결과가 나온다. 집계함수를 사용해서 행이 선택되어도 실 데이터는 결국 `null`이다. 

3. NULLIF

   표현식 1이 표현식 2와 같으면 NULL을, 같지 않으면 표현식 1을 리턴. 특정 값을 `null` 로 대체하는 경우에 유용하게 사용가능

   ```sql
   NULLIF (표현식1, 표현식2)
   ```

4. COALESCE 함수

   표현식을 몇개 넣을지 한정하지 않으며, 임의의 표현식이 쭉 괄호`()`안에 나열 되었을 때, NULL 이 아닌 최초의 표현식을 리턴하고, 모든 표현식이 NULL이면 그냥 NULL을 리턴

   ```sql
   COALESCE (표현식1, 표현식2, …)
   ```



## 집계함수 Aggregate Function

> 다중행 함수중 집계함수는 여러 행들이 모여 하나의 결과를 돌려준다.

1. 여러 행이 모여 하나의 행 결과를 출력
2. GROUP BY 절은 행들을 **소그룹화** 한다.(기준 열에 대해 같은 데이터 값끼리 그룹으로 묶고 묶은 행의 집합에 대해 그룹 함수 연산이 필요할 때 사용하는 절)
3.  SELECT 절, HAVING 절(묶은 그룹들에 대해 조건이 필요할 때), ORDER BY 절에 사용할 수 있다.
4. 집계함수에는 이 장에서 정리하는 그룹함수 뿐만 아니라 `ROLLUP, CUBE, GROUPING SETS` 같은 그룹함수와, 다양한 분석 기능을 가진 `윈도우 함수`가 있다.



#### 그룹함수 종류

![](http://www.dbguide.net/publishing/img/knowledge/SQL_193.jpg)



#### 기본 문법

````SQL
# []안의 절은 필수 x
SELECT 그룹함수(열 이름)
FROM 테이블 이름
[WHERE 조건식]
[ORDER BY 열이름]
````

1. COUNT : 지정한 열의 행 개수를 세는 함수

   ```sql
   # 열 이름 대신(*)를 사용하면 모든 행의 개수를 센다.
   SELECT COUNT(salary) AS salary행수
   FROM employees;
   ```

2. SUM, AVG : 열의 합계와 평균을 구하는 함수

   ```sql
   SELECT SUM(salary) 합계, AVG(salary) 평균,  SUM(salary)/AVG(salary) 평균계산
   FROM employees;
   ```

3. MIN, MAX : 열의 최솟값과 최댓값을 구하는 함수

   ```SQL
   # 숫자 뿐만 아니라 문자 데이터 타입에도 적용 가능하다.
   SELECT MAX(salary) 최댓값, MIN(salary) 최솟값,
          MAX(first_name) 최대문자값,
          MIN(first_name) 최소문자값
   FROM employees;
   ```



#### GROUP BY 그룹으로 묶기

지금까지는 하나의 열을 기준으로 다른 행에 대입했다면, GROUP BY는 같은 데이터 값을 갖는 행끼리 묶어서 그룹화한 다음, 그에 해당하는 다른 열의 데이터 집합을 그룹함수에 전달하여 연산할 수 있다.

1. 코드 패턴

   ```SQL
   # select절에 열 이름과 그룹함수를 함께 기술한 후, GROUP BY절을 사용해야한다
   SELECT A, SUM(B)
   FROM 테이블 이름
   [WHERE 조건식]
   GROUP BY A
   [ORDER BY 열이름];
   ```

   > 위의 코드 패턴 예제는 A열 데이터 중, 같은 값을 가진 데이터 끼리 그룹한 행 위치의 B열 데이터를 합하는 예제이다.

2. 주의할 점: 

   - `SELECT`절에 기준 열, 그룹함수가 지정되면 `GROUP BY`에 반드시 기준 열을 입력해야한다.
   - WHERE에 행의 조건을 붙이면 그룹되기 전 적용된다.
   - SELECT절에 그룹함수 사용하지 않아도 GROUP BY 절 만으로도 사용할 수 있다.

3. 예제 코드

   ```sql
   # 예제 1(WHERE조건식과 GROUP BY 적용되는 패턴 익히기)
   SELECT job_id 직무, SUM(salary) 직무별_총급여,
          AVG(salary) 직무별_평균급여
   FROM employees
   WHERE employee_id >= 10
   GROUP BY job_id
   ORDER BY 직무별_총급여 DESC, 직무별_평균급여;
   ```

   ```sql
   # 예제 2(그룹에 대한 그룹/ 대그룹,중그룹,소그룹)
   SELECT job_id 대그룹,
          manager_id 중그룹,
          SUM(salary) 그룹된_총급여,
          AVG(salary) 그룹된_평균급여
   FROM employees
   WHERE employee_id >= 10
   GROUP BY job_id, manager_id
   ORDER BY 그룹된_총급여 DESC, 그룹된_평균급여
   ```

   > job_id 별로 한 번 그룹화 하고, manager_id 별로 다시 한번 그룹화



#### HAVING 연산된 그룹함수 결과에 조건 적용하기

그룹화된 값에 조건식을 적용할 때 사용한다. WHERE 절에서는 그룹함수를 사용할 수 없으니 HAVING절을 사용해서 그룹함수를 통해 나온 결과 값중에서 조건에 만족하는 값만 출력시킨다.

1. 코드 패턴

   ```SQL
   # group by절 다음에 사용하는 것이 가독성 좋음
   SELECT 열 이름, 그룹함수(열 이름)
   FROM 테이블 이름
   [WHERE 조건식]
   GROUP BY A
   HAVING 조건식
   [ORDER BY 열이름];
   ```

2. 예제 코드

   ```SQL
   # 이전의 group by절의 예제에서 합계연봉 30000인 그룹 조건 추가
   SELECT job_id 직무, SUM(salary) 직무별_총급여,
          AVG(salary) 직무별_평균급여
   FROM employees
   WHERE employee_id >= 10
   GROUP BY job_id
   HAVING SUM(salary) > 30000
   ORDER BY 직무별_총급여 DESC, 직무별_평균급여;
   ```

   



#### ORDER BY 정렬 요약

ORDER BY 절은 SQL 문장으로 조회된 데이터들을 다양한 목적에 맞게 특정 칼럼을 기준으로 정렬하여 출력하는데 사용한다.  `AS` 통해 만든 별칭이나 칼럼 순서를 나타내는 `정수` 로도 지정 가능하다. 항상 SQL 문장의 **제일 마지막**에 위치한다.

1. 기본은 오름차순 `ASC`

2. 내림차순 지정은 `ORDER BY 기준열 이름 DESC`

3. 문장 실행 순서:

   ```sql
   5. SELECT 칼럼명 [ALIAS명] 
   1. FROM 테이블명 
   2. WHERE 조건식 
   3. GROUP BY 칼럼(Column)이나 표현식 
   4. HAVING 그룹조건식 
   6. ORDER BY 칼럼(Column)이나 표현식;
   ```

4. TOP N 쿼리

   Oracle에서 순위가 높은 N개의 행을 추출하기 위해 `ORDER BY` 절과 WHERE 절의 `ROWNUM `조건을 같이 사용하는 경우가 있는데 이 두 조건으로는 원하는 결과를 얻을 수 없다. 

   그래서 먼저 데이터 정렬을 수행한 후 메인쿼리에서 ROWNUM 조건을 사용해야 한다.

   ```sql
   # 예제
   SELECT ename, sal 
   FROM (SELECT ename, sal FROM emp ORDER BY sal DESC)
   WHERE ROWNUM < 4 ;
   ```

   > 정리하자면, 순위 높은 N개의 행을 추출하려고 하는데, 정렬을 해주는 문장인 ORDER BY절은 가장 마지막에 처리를 해주니 원하는 결과가 나오지 않는다. 그래서 테이블에 넣기 전에 정렬을 해준 후 메인쿼리 문에 넣어 준다.

   