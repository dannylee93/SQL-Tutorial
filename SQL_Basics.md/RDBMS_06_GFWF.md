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

