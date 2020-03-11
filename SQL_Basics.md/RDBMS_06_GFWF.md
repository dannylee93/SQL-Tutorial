# Group Function & Window Function

> 함수는 3가지 종류가 있다. 집계함수 / 그룹함수 / 윈도우 함수 이다.  앞 서 정리했던 집계함수에 대한 내용에 추가하여 그룹함수와 윈도우 함수를 정리한다.



## Function (재정리)

> 함수란 사용자가 입력한 값 `X` 를 넣으면 정해 놓은 출력 값 `Y`가 나오는 개념이다.

DB guide 에서는 내장함수와 사용자 정의 함수로 나누고, 그 내장함수에서 단일행 함수와 다중행 함수로 나눈다. **단일행 함수**는 함수의 입력값이 단일행 값인 함수이며, **다중행 함수**는 `집계함수, 그룹함수, 윈도우 함수`로 또 나눌 수 있다.

#### 사용하는 목적

1. `단일 행 함수` : 데이터의 값을 계산하거나 조작

2. `그룹 함수` : 행의 그룹에 대해 계산하거나 요약

3. 열의 **데이터 타입** 변경.

4. **중요 포인트**: 

   단일 행 함수는 한개 열로 하나의 열을 뽑아내고, 그룹 함수는 여러 열로 하나의 새로운 행을 만든다.



## Group Function

> COUNT, SUM, AVG, MAX, MIN 외 각종 집계 함수들이 포함되어 있다.

결산 개념의 업무를 가지는 원가나 판매 시스템의 경우는 소계, 중계, 합계, 총 합계 등 여러 레벨의 결산 보고서를 만드는 것이 중요 업무 중의 하나이다. 

*그룹 함수를 사용한다면* 하나의 SQL로 테이블을 한 번만 읽어서 빠르게 원하는 리포트를 작성할 수 있다. 



### 그룹함수 종류

1. *ROLLUP 함수*
2. *CUBE 함수*
3. *GROUPING SETS 함수*



#### ROLLUP 함수

Grouping Columns의 List는 Subtotal을 생성하기 위해 사용되어지며, Grouping Columns의 수를 **N**이라고 했을 때 **N+1 Level**의 Subtotal이 생성된다.

 ROLLUP의 인수는 계층 구조이므로 **인수 순서가 바뀌면 수행 결과도 바뀌게** 되므로 인수의 순서에도 주의해야 한다.

- 일반적인 GROUP BY 절 사용했을 때

  ```SQL
  SELECT DNAME, JOB, COUNT(*) "Total Empl", SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY DNAME, JOB;
  ```

  > 부서명과 업무명을 기준으로 사원수와 급여 합을 집계한 일반적인 GROUP BY SQL 문장

- GROUP BY 절 + ORDER BY 절 사용했을 때

  ```SQL
  SELECT DNAME, JOB, COUNT(*) "Total Empl", SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY DNAME, JOB 
  ORDER BY DNAME, JOB;
  ```

  >  부서명과 업무명을 기준으로 집계한 일반적인 GROUP BY SQL 문장에 ORDER BY 절을 사용함으로써 부서, 업무별로 정렬이 이루어진다.

- 이제 ROLLUP 함수 사용해보기

  ```SQL
  SELECT DNAME, JOB, COUNT(*) "Total Empl", SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY ROLLUP (DNAME, JOB);
  ```

  > 부서명과 업무명을 기준으로 집계한 일반적인 GROUP BY 문장에 ROLLUP 함수를 사용

  위의 실행결과를 보자

  ```SQL
  [실행 결과] 
  DNAME JOB Total Empl Total Sal
  ---------- -------- --------- -------- 
  SALES CLERK 1 950 
  SALES MANAGER 1 2850 
  SALES SALESMAN 4 5600 
  SALES 6 9400 RESEARCH 
  CLERK 2 1900 RESEARCH 
  ANALYST 2 6000 RESEARCH 
  MANAGER 1 2975 RESEARCH 5 
  10875 ACCOUNTING CLERK 1 
  1300 ACCOUNTING MANAGER 1
  2450 ACCOUNTING PRESIDENT 1 
  5000 ACCOUNTING 3 8750 
  14 29025 13개의 행이 선택되었다.
  ```

  - `L1` - GROUP BY 수행시 생성되는 표준 집계 (9건) 
  - `L2` - DNAME 별 모든 JOB의 SUBTOTAL (3건) 
  - `L3` - GRAND TOTAL (마지막 행, 1건)
  - L1, L2, L3 계층 내 **정렬을 위해서는 별도의 ORDER BY 절**을 사용해야 한다.

- ROLLUP 함수 + ORDER BY 절 사용

  ```SQL
  SELECT DNAME, JOB, COUNT(*) "Total Empl", SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY ROLLUP (DNAME, JOB) 
  ORDER BY DNAME, JOB 
  ;
  ```

  > 이전 ROLLUP 코드에 ORDER BY절을 추가로 적어 부서, 업무별 정렬한다.

- GROUPING 함수 사용

  ROLLUP, CUBE, GROUPING SETS 등 새로운 그룹 함수를 지원하기 위해 GROUPING 함수가 추가되었다.

  - 특징:
    - ROLLUP이나 CUBE에 의한 소계가 계산된 결과에는 GROUPING(EXPR) = 1 이 표시
    - 그 외의 결과에는 GROUPING(EXPR) = 0 이 표시

  GROUPING 함수와 CASE/DECODE를 이용해, **소계를 나타내는 필드에 원하는 문자열을 지정**할 수 있어, 보고서 작성시 유용하게 사용할 수 있다.

  ```SQL
  SELECT DNAME, 
         GROUPING(DNAME), 
         JOB, 
         GROUPING(JOB), 
         COUNT(*) "Total Empl", 
         SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY ROLLUP (DNAME, JOB)
  ;
  ```

  > ROLLUP 함수를 추가한 집계 보고서에서 집계 레코드를 구분할 수 있는 GROUPING 함수가 추가된 SQL 문장

- GROUPING 함수 + CASE (DECODE)사용

  ```SQL
  SELECT 
        CASE GROUPING(DNAME) WHEN 1 THEN 'All Departments' ELSE DNAME END 
             AS DNAME, 
        CASE GROUPING(JOB) WHEN 1 THEN 'All Jobs' ELSE JOB END AS JOB,
        COUNT(*) "Total Empl", 
        SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY ROLLUP (DNAME, JOB)
  ;
        
  # Oracle의 경우는 DECODE 함수를 사용해서 좀더 짧게 표현할 수 있다. 
  
  SELECT 
        DECODE(
               GROUPING(DNAME), 1, 'All Departments', DNAME) AS DNAME, 
        DECODE(
               GROUPING(JOB), 1, 'All Jobs', JOB) AS JOB, 
        COUNT(*) "Total Empl", 
        SUM(SAL) "Total Sal"
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY ROLLUP (DNAME, JOB)
  ;
  ```

- ROLLUP 함수 일부 사용

  ```SQL
  SELECT 
        CASE GROUPING(DNAME) WHEN 1 THEN 'All Departments' ELSE DNAME END 
             AS DNAME, 
        CASE GROUPING(JOB) WHEN 1 THEN 'All Jobs' ELSE JOB END 
             AS JOB,
        COUNT(*) "Total Empl", 
        SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY DNAME, ROLLUP(JOB)
  ;
  ```

  > 결과는 마지막 ALL DEPARTMENTS & ALL JOBS 줄만 계산이 되지 않았다. ROLLUP이 JOB 칼럼에만 사용되었기 때문에 DNAME에 대한 집계는 필요하지 않기 때문이다.



#### CUBE 함수

큐브 는 결합 가능한 모든 값에 대하여 다차원 집계를 생성한다.

롤업 함수와 마찬가지로 `GROUP BY`절에서 사용하며, 기술한 순서와 상관없이 각 그룹별 소계, 중계 총계가 다 나온다. 

```SQL
SELECT 
      CASE GROUPING(DNAME) WHEN 1 THEN 'All Departments' ELSE DNAME END 
           AS DNAME, 
      CASE GROUPING(JOB) WHEN 1 THEN 'All Jobs' ELSE JOB END 
           AS JOB,
      COUNT(*) "Total Empl", 
      SUM(SAL) "Total Sal" 
FROM EMP, DEPT 
WHERE DEPT.DEPTNO = EMP.DEPTNO 
GROUP BY CUBE (DNAME, JOB) ;
```

> GROUP BY 절에서 CUBE를 사용한데 집중하면 된다.



#### GROUPING SETS

GROUP BY SQL 문장을 여러 번 반복하지 않아도 GROUPING SETS를 이용해 더욱 다양한 소계 집합을 만들 수 있다.

ROUPING SETS에 표시된 **인수들에 대한 개별 집계**를 구할 수 있으며, 이때 표시된 인수들 간에는 계층 구조인 ROLLUP과는 달리 평등한 관계이므로 **인수의 순서가 바뀌어도 결과는 같다**.

- 일반 그룹함수를 이용한 SQL

  ```SQL
  SELECT DNAME, 'All Jobs' JOB, 
         COUNT(*) "Total Empl", 
         SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY DNAME 
  
  UNION ALL 
  
  SELECT 'All Departments' DNAME, JOB, 
          COUNT(*) "Total Empl", 
          SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY JOB 
  ;
  ```

  > 일반 그룹함수를 이용하여 부서별, JOB별 인원수와 급여 합 구하기. 실행 결과는 정렬이 되어 있지 않다.

- GROUPING SETS 사용 SQL

  ```SQL
  SELECT 
        DECODE(GROUPING(DNAME), 1, 'All Departments', DNAME) AS DNAME,
        DECODE(GROUPING(JOB), 1, 'All Jobs', JOB) AS JOB, 
        COUNT(*) "Total Empl", 
        SUM(SAL) "Total Sal" 
  FROM EMP, DEPT 
  WHERE DEPT.DEPTNO = EMP.DEPTNO 
  GROUP BY GROUPING SETS (DNAME, JOB);
  ```

  >  일반 그룹함수를 GROUPING SETS 함수로 변경하여 부서별, JOB별 인원수와 급여 합을 구했다. 

- 주의할 점:

  GROUPING SETS 함수 사용시 **UNION ALL**을 사용한 일반 그룹함수를 사용한 SQL과 **같은 결과**를 얻을 수 있으며, 괄호로 묶은 집합 별로(괄호 내는 계층 구조가 아닌 하나의 데이터로 간주함) 집계를 구할 수 있다.



## Window Function

> 기존에 연산은 대부분 칼럼과 칼럼간의 비교/연산 이였다. 행과 행 간의 관계를 쉽게 정의하기 위한 함수가 WINDOW FUNCTION 이다.



### 윈도우 함수 종류

크게 다섯 개의 그룹으로 사용할 수 있다.

1. **RANK(순위)** 관련 : RANK, DENSE_RANK, ROW_NUMBER
2. 그룹 내 **집계(AGGREGATE)** 관련 : SUM, MAX, MIN, AVG, COUNT
3. 그룹 내 **행 순서** 관련 : FIRST_VALUE, LAST_VALUE, LAG, LEAD
4. 그룹 내 **비율** 관련 : CUME_DIST, PERCENT_RANK, NTILE, RATIO_TO_REPORT
5. 선형 분석을 포함한 통계 분석 관련 함수



### (1) 그룹 내 순위 함수

- RANK 함수

   `RANK`함수는 `ORDER BY`를 포함한 쿼리에서 특정 항목(칼럼)에 대한 순위를 구하는 함수이다.

  ```SQL
  SELECT JOB, 
         ENAME,
         SAL,
         RANK( ) OVER (ORDER BY SAL DESC) ALL_RANK,
         RANK( ) OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK 
  FROM EMP;
  ```

  > 사원 데이터에서 급여가 높은 순서와 JOB 별로 급여가 높은 순서를 같이 출력

  업무 구분이 없는 `ALL_RANK 칼럼`에서 FORD와 SCOTT, WARD와 MARTIN은 동일한 SALARY이므로 **같은 순위를 부여**한다. 그리고 업무를 `PARTITION`으로 구분한 JOB_RANK의 경우 같은 **업무 내 범위에서만 순위를 부여**한다. 

  ```SQL
  SELECT JOB, 
         ENAME, 
         SAL, 
         RANK() OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK 
  FROM EMP;
  ```

  > 앞의 SQL문의 결과는 JOB과 SALARY 기준으로 정렬이 되어있지 않다. 이번 코드는 전체 SALARY 순위를 구하는 ALL_RANK 칼럼은 제외하고, 업무별로 SALARY 순서를 구하는 JOB_RANK

- DENSE_RANK 함수

  일반 랭크함수와 비슷하지만, 이 함수는 동일한 순위는 하나의 건수로 취급한다.

  ```SQL
  SELECT JOB, 
         ENAME, 
         SAL,
         RANK( ) OVER (ORDER BY SAL DESC) RANK,
         DENSE_RANK( ) OVER (ORDER BY SAL DESC) DENSE_RANK 
  FROM EMP;
  ```

  > 5번 째 SELECT 문에서 사용한 결과로, 동일한 순위는 같은 순위를 부여한다.

- ROW_NUMBER 함수

  RANK나 DENSE_RANK 함수가 동일한 값에 대해서는 동일한 순위를 부여하는데 반해, 이 함수는 동일한 값이라도 고유한 순위를 부여한다.

  ```SQL
  SELECT JOB, 
         ENAME,
         SAL,
         RANK( ) OVER (ORDER BY SAL DESC) RANK,
         ROW_NUMBER() OVER (ORDER BY SAL DESC) ROW_NUMBER 
  FROM EMP;
  ```

  > 사원데이터에서 급여가 높은 순서와, 동일한 순위를 인정하지 않는 등수도 같이 출력한다.

### (2) 일반 집계 함수

- SUM 함수

  ```sql
  SELECT MGR, ENAME, SAL, SUM(SAL) OVER (PARTITION BY MGR) MGR_SUM 
  FROM EMP; 
  
  PARTITION BY MGR 
  ```

  >  사원들의 급여와 같은 매니저를 두고 있는 사원들의 SALARY 합 구하기

  ```sql
  SELECT MGR, 
         ENAME,
         SAL,
         SUM(SAL) OVER (
                        PARTITION BY MGR ORDER BY SAL RANGE UNBOUNDED
                        PRECEDING
                        ) as MGR_SUM 
  FROM EMP RANGE UNBOUNDED PRECEDING : 
  # 현재 행을 기준으로 파티션 내의 첫 번째 행까지의 범위를 지정한다.
  ```

  > OVER 절 내에 ORDER BY 절을 추가해 파티션 내 데이터를 정렬하고 이전 SALARY 데이터까지의 누적값을 출력한다.

- MAX 함수

  ```sql
  SELECT MGR, 
         ENAME,
         SAL,
         MAX(SAL) OVER (PARTITION BY MGR) as MGR_MAX 
  FROM EMP;
  ```

  > 사원들의 급여와 같은 매니저를 두고 있는 사원들의 SALARY 중 최대값을 같이 구한다.

- MIN 함수

  ```sql
  SELECT MGR, 
         ENAME, 
         HIREDATE, 
         SAL,
         MIN(SAL) OVER(PARTITION BY MGR ORDER BY HIREDATE) as MGR_MIN 
  FROM EMP;
  ```

  > 사원들의 급여와 같은 매니저를 두고 있는 사원들을 입사일자를 기준으로 정렬하고, SALARY 최소값을 같이 구한다.

- AVG 함수

  ```sql
  SELECT MGR, 
         ENAME,
         HIREDATE,
         SAL,
         ROUND (AVG(SAL) OVER (
                                PARTITION BY MGR 
                                ORDER BY HIREDATE ROWS
                                BETWEEN 1 PRECEDING
                                AND 1 FOLLOWING)) as MGR_AVG 
  FROM EMP;
  
  ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING
  ```

  > EMP 테이블에서 같은 매니저를 두고 있는 사원들의 평균 SALARY를 구하는데, 조건은 같은 매니저 내에서 자기 바로 앞의 사번과 바로 뒤의 사번인 직원만을 대상으로 한다.

- COUNT 함수

  COUNT 함수와 파티션별 ROWS 윈도우를 이용해 **원하는 조건에 맞는 데이터에 대한 통계값**을 구할 수 있다.

  ```sql
  SELECT ENAME, 
         SAL,
         COUNT(*) OVER (
                         ORDER BY SAL RANGE 
                         BETWEEN 50 PRECEDING AND 150 FOLLOWING
                        ) as SIM_CNT 
  FROM EMP;
  
  RANGE BETWEEN 50 PRECEDING AND 150 FOLLOWING 
  ```

  > 사원들을 급여 기준으로 정렬하고, 본인의 급여보다 50 이하가 적거나 150 이하로 많은 급여를 받는 인원수를 출력



### (3) 그룹 내 행 순서 함수

- FIRST_VALUE 함수

  파티션 별 윈도우에서 **가장 먼저 나온 값**을 구한다*(SQL - Server에서는 지원하지 않는다.)*

  ```sql
  SELECT DEPTNO,
         ENAME,
         SAL,
         FIRST_VALUE(ENAME) OVER (
                                   PARTITION BY DEPTNO ORDER BY SAL
                                   DESC ROWS UNBOUNDED PRECEDING
                                  ) as DEPT_RICH 
  FROM EMP; 
  
  RANGE UNBOUNDED PRECEDING 
  # 현재 행을 기준으로 파티션 내의 첫 번째 행까지의 범위를 지정한다.
  ```

  > 부서별 직원들을 연봉이 높은 순서부터 정렬하고, 파티션 내에서 가장 먼저 나온 값을 출력

- LAST_VALUE 함수

  이 함수를 이용해 파티션 별 윈도우에서 **가장 나중에 나온 값**을 구한다. *(SQL - Server에서는 지원하지 않는다.)*

  ```sql
  SELECT DEPTNO, 
         ENAME,
         SAL,
         LAST_VALUE(ENAME) OVER (
                                 PARTITION BY DEPTNO ORDER BY SAL 
                                 DESC ROWS BETWEEN CURRENT ROW 
                                 AND UNBOUNDED FOLLOWING) as DEPT_POOR 
  FROM EMP;
  
  ROWS BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING
  # 현재 행을 포함해서 파티션 내의 마지막 행까지의 범위를 지정한다.
  ```

  > 부서별 직원들을 연봉이 높은 순서부터 정렬하고, 파티션 내에서 가장 마지막에 나온 값을 출력한다.

- LAG 함수

  파티션별 윈도우에서 **이전 몇 번째 행의 값**을 가져올 수 있다. *(SQL - Server에서는 지원하지 않는다.)*

  ```sql
  SELECT ENAME,  
         HIREDATE,
         SAL,
         LAG(SAL) OVER (ORDER BY HIREDATE) as PREV_SAL 
  FROM EMP
  WHERE JOB = 'SALESMAN' 
  ;
  ```

  > 직원들을 입사일자가 빠른 기준으로 정렬을 하고, 본인보다 입사일자가 한 명 앞선 사원의 급여를 본인의 급여와 함께 출력한다.

- LEAD 함수

  파티션별 윈도우에서 **이후 몇 번째 행의 값**을 가져올 수 있다. *(SQL - Server에서는 지원하지 않는다.)*

  ```sql
  SELECT ENAME, HIREDATE, LEAD(HIREDATE, 1) OVER (ORDER BY HIREDATE) as "NEXTHIRED" FROM EMP;
  ```

  > 직원들을 입사일자가 빠른 기준으로 정렬을 하고, 바로 다음에 입사한 인력의 입사일자를 함께 출력



### (4) 그룹 내 비율 함수

- RATIO_TO_REPORT 함수

  파티션 내 **전체 SUM(칼럼)값에 대한 행별 칼럼 값의 백분율을 소수점**으로 구할 수 있다. 결과 값은 > 0 & <= 1 의 범위를 가진다. 그리고 **개별 RATIO의 합을 구하면 1**이 된다.

  ```sql
  SELECT ENAME,
         SAL,
         ROUND(RATIO_TO_REPORT(SAL) OVER (), 2) as R_R 
  FROM EMP
  WHERE JOB = 'SALESMAN';
  ```

  > JOB이 SALESMAN인 사원들을 대상으로 전체 급여에서 본인이 차지하는 비율을 출력

  실행 결과에서 전체 값은 1650 + 1250 + 1250 + 1500 = 5600이 되고, RATIO_TO_REPORT 함수 연산의 분모로 사용된다. 그리고 개별 RATIO의 전체 합을 구하면 1이 되는 것을 확인할 수 있다.

- PERCENT_RANK 함수

   파티션별 윈도우에서 **제일 먼저 나오는 것을 0**으로, **제일 늦게 나오는 것을 1**로 하여, 값이 아닌 행의 **순서별 백분율**을 구한다. 결과 값은 >= 0 & <= 1 의 범위를 가진다. 

  ```sql
  SELECT DEPTNO, 
         ENAME,
         SAL,
         PERCENT_RANK() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC) as P_R 
  FROM EMP;
  ```

  > 같은 부서 소속 사원들의 집합에서 본인의 급여가 순서상 몇 번째 위치쯤에 있는지 0과 1 사이의 값으로 출력

  DEPTNO 10의 경우 3건이므로 구간은 2개가 된다. 0과 1 사이를 2개의 구간으로 나누면 0, 0.5, 1이 된다. DEPTNO 20의 경우 5건이므로 구간은 4개가 된다. 0과 1 사이를 4개의 구간으로 나누면 0, 0.25, 0.5, 0.75, 1이 된다. DEPTNO 30의 경우 6건이므로 구간은 5개가 된다. 0과 1 사이를 5개의 구간으로 나누면 0, 0.2, 0.4, 0.6, 0.8, 1이 된다. 

  

- CUME_DIST 함수

  윈도우의 전체건수에서 현재 행보다 작거나 같은 건수에 대한 **누적백분율**을 구한다. 결과 값은 > 0 & <= 1 의 범위를 가진다. 

  ```sql
  SELECT DEPTNO, 
         ENAME,
         SAL,
         CUME_DIST() OVER (PARTITION BY DEPTNO ORDER BY SAL DESC)
                           as CUME_DIST 
  FROM EMP;
  ```

  > 같은 부서 소속 사원들의 집합에서 본인의 급여가 누적 순서상 몇 번째 위치쯤에 있는지 0과 1 사이의 값으로 출력한다.

  - DEPTNO가 10인 경우 윈도우가 전체 3건이므로 0.3333 단위의 간격을 가진다. 즉, 0.3333, 0.6667, 1의 값이 된다. 

  - DEPTNO가 20인 경우 윈도우가 전체 5건이므로 0.2000 단위의 간격을 가진다. 즉, 0.2000, 0.4000, 0.6000, 0.8000, 1의 값이 된다.

  - DEPTNO가 30인 경우 윈도우가 전체 6건이므로 0.1667 단위의 간격을 가진다. 즉, 0.1667, 0.3333, 0.5000, 0.6667, 0.8333, 1의 값이 된다. 

  * `*`표시가 있는 SCOTT, FORD와 ** 표시가 있는 WARD, MARTIN의 경우 ORDER BY SAL에 의해 SAL 이 같으므로 같은 ORDER로 취급한다.

- NTILE 함수

  파티션별 전체 건수를 ARGUMENT 값으로 N 등분한 결과를 구할 수 있다.

  ```sql
  SELECT ENAME, 
         SAL,
         NTILE(4) OVER (ORDER BY SAL DESC) as QUAR_TILE 
  FROM EMP
  ```

  > 전체 사원을 급여가 높은 순서로 정렬하고, 급여를 기준으로 4개의 그룹으로 분류

  위 예제에서 NTILE(4)의 의미는 14명의 팀원을 4개 조로 나눈다는 의미이다. 전체 14명을 4개의 집합으로 나누면 몫이 3명, 나머지가 2명이 된다. 나머지 두 명은 앞의 조부터 할당한다. 즉, 4명 + 4명 + 3명 + 3명으로 조를 나누게 된다.