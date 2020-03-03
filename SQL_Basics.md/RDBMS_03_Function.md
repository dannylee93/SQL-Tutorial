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