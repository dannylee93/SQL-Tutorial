# Join

> 데이터는 테이블에 흩어져 저장되어 있으므로, 사용자가 원하는 형태로 데이터를 조작해야하는데 이 때 사용하는 것이 조인.



#### 조인 기법의 종류

| 조인 기법                  | 설명                                                     |
| -------------------------- | -------------------------------------------------------- |
| 곱집합(cartesian product)  | 가능한 모든 행을 조인한다.                               |
| **동등 조인(equi join)**   | 조인 조건이 정확히 일치하는 경우에 결과를 출력한다.      |
| 비동등 조인(non equi join) | 조인 조건이 정확히 일치하지 않는 경우에 결과를 출력한다. |
| **외부 조인(outer join)**  | 조인 조건이 정확히 일치하지 않아도 모든 결과를 출력한다. |
| **자체 조인(self join)**   | 자체 테이블에서 조인하고자 할 때 사용한다.               |



#### 조인을 사용할 때 규칙

- SELECT 절에는 **출력할 열**의 이름을 적는다.
- FROM 절에는 접근하려는 **테이블의 이름**을 기술
- WHERE 절에는 **조인 조건**을 기술
- FROM 절 외의 절에는 조회의 명확성을 위해 열 이름 앞에 테이블 이름을 붙입니다.(각 테이블 마다 칼럼 명이 동일할 수 있으니까 오해 방지를 위해 꼭 필요)



## 동등 조인 Equi-Join

> 양쪽 테이블에서 조건이 일치하는 행만 가져오는 가장 일반적인 조인으로 `=`를 사용하여 조건 값이 정확하게 일치하는 행만 가져온다.(== Inner Join 이라고도 함)



#### 문법

```SQL
SELECT A.열 이름1, B.열이름2, ....
FROM 테이블A as A, 테이블B as B
WHERE A.열 이름1 = B.열이름2;
```

> WHERE절에서 `=` 등호 연산자로 동등조인을 실행 했다.

```SQL
# 계속 조인 조건을 추가하려면 WHERE절 다음에 AND 연산자를 사용한다.
SELECT A.열 이름1, B.열이름2, ....
FROM 테이블A as A, 테이블B as B
WHERE A.열 이름1 = B.열이름2

AND A.열 이름3 = B.열이름4
.
.
;
```

#### 비동등 조인 Non - Equi join

Non EQUI(비등가) JOIN은 두 개의 테이블 간에 칼럼 값들이 서로 정확하게 일치하지 않는 경우에 사용된다. Non EQUI JOIN의 경우에는 `=` 기호가 아닌 `Between, >, >=, <, <= 등` 연산자들을 사용하여 JOIN을 수행하는 것이다.

- 예시:

  ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_198.jpg)

  위의 이미지처럼 등급에 따라 최저-최대 급여로 나눈 테이블과 사원 급여를 JOIN하고 싶을 때다.

  사원테이블에 급여등급을 적용하고 싶은데 `=`로 넣을 수 없다.

  ```sql
  SELECT E.ENAME 사원명, 
         E.SAL 급여, 
         S.GRADE 급여등급 
  FROM EMP E, SALGRADE S 
  WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL
  ;
  ```

  위와같이 실행했을 땐 아래와 같이 결과가 나온다.

  ```sql
  사원명 급여 급여등급 
  ------ ---- ------ 
  SMITH 800 1 
  JAMES 950 1 
  ADAMS 1100 1 
  WARD 1250 2 
  MARTIN 1250 2
  MILLER 1300 2 
  TURNER 1500 3 
  ALLEN 1600 3 
  CLARK 2450 4 
  BLAKE 2850 4 
  JONES 2975 4 
  ...
  14개의 행이 선택되었다.
  ```

  

## 외부 조인

> 동등 조인은 데이터 값이 정확히 일치하는 값만 출력하지만 데이터 값이 일치하지 않으면 결과가 조회되지 않는다(null).  여기서 외부조인은 조건을 만족하지 않는 행도 모두 출력하기 위한 기법이다.



#### 외부조인의 특징

- 동등조인으로 누락되고 있는 행을 출력하기 위해 사용
- 외부조인은 `+` 기호를 사용한다.
- `+` 기호는 조인할 행이 없는, 데이터 값이 부족한 테이블의 열 이름 뒤에 기술
- `+` 기호는 외부조인하려는 **한쪽에만** 기술할 수 있다.
- `+` 기호를 사용하면 데이터 값이 부족한 테이블에 null값을 갖는 행이 생성되어 데이터 값이 충분한 테이블의 행에 `null` 값을 가진 채 조인된다.

#### 예시

```SQL
SELECT A.employee_id, 
	   A.first_name,
	   A.last_name,
	   B.department_id,
	   B.department_name
FROM employees AS A, departments AS B
WHERE A.department_id = B.department_id(+)
ORDER BY A.employee_id;
```

> 만약 `+` 기호를 반대 테이블에 기술하면? B테이블에 없는 A테이블의 데이터들이 null로 표시되어 출력된다.



## 자체 조인 Self Join

자기 자신의 테이블을 조인하는 것을 자체조인 이라고 한다.

예를 들어, 직원 정보 테이블중에 담당 매니저의 정보를 담고 있는 *manager_id* 열이 있을 때 현재 테이블에는 *직원코드(employee_id)* 만 가지고 있으므로, 결국 해당 테이블 내에서 조인을 통해 새로운 칼럼을 생성해야 한다.



#### 예시코드

```SQL
# 셀프조인은 같은 테이블이라도 별칭으로 나눠줘야 한다.
SELECT A.employee_id,
       A.first_name,
       A.last_name,
       A.manager_id,
       B.first_name||' '||B.last_name AS manager_name
FROM employees AS A, employees AS B
WHERE A.manager_id = B.employee_id
ORDER BY A.employee_id
```



#### 셀프조인에서 계층형 질의 Hierarchical Query

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_206.jpg)

계층형 구조에서 A의 하위 사원은 B, C이고 C의 하위 사원은 D,E가 있다. 이러한 계층형 구조를 데이터로 표현한 것이 맨 오른쪽 3번 샘플데이터 이다.



#### 오라클에서 계층형 질의 실전

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_207.jpg)

- `START WITH`는 계층 구조 전개의 시작 위치 구문

- `CONNECT BY`는 다음에 전개될 자식 데이터를 지정하는 구문

  - `PRIOR` 자식=부모 형태: 

    계층구조에서 (자식>>부모) 방향으로 순방향 전개 (* 반대는 역방향 전개)

  - `NOCYCLE` :

    데이터를 전개하면서 이미 나타났던 동일한 데이터가 전개 중에 있으면 이것을 **CYCLE** 이라 하는데, 사이클이 발생하면 런타임 오류 가 발생한다. 여기서 **NOCYCLE** 은 사이클이 발생한 이 후에 데이터는 전개하지 않는다.

- `ORDER SIBLINGS BY` 형제 노드(동일 LEVEL) 사이에서 정렬을 수행한다.

- `WHERE` :모든 전개를 수행한 후에 지정된 조건을 만족하는 데이터만 추출한다.(필터링)

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_208.jpg)

- 예제 코드 : 

  ```SQL
  # A로 부터 순방향 전개
  SELECT LEVEL, 
         LPAD(' ', 4 * (LEVEL-1)) || 사원 사원, 
         관리자, 
         CONNECT_BY_ISLEAF ISLEAF 
  FROM 사원 
  START WITH 관리자 IS NULL 
  CONNECT BY PRIOR 사원 = 관리자;
  ```

  ```SQL
  # 실행 결과
  LEVEL 사원 관리자 ISLEAF
  ----- -------- ----- ------ 
  1 A 0 2
  B A 1 2 
  C A 0 3 
  D C 1 3 
  E C 1
  ```

  > A는 루트 데이터(== 레벨 1), A의 하위데이터인 B, C는 레벨 2, D,E는 레벨 3 (리프 데이터는 B,D,E)

  ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_209.jpg)

  ```SQL
  # 사원 D로 부터 상위 관리자를 찾는 역방향 전개
  SELECT LEVEL, 
         LPAD(' ', 4 * (LEVEL-1)) || 사원 사원,
         관리자, 
         CONNECT_BY_ISLEAF ISLEAF 
  FROM 사원 START WITH 사원 = 'D' 
  CONNECT BY PRIOR 관리자 = 사원;
  ```

  ```SQL
  # 실행결과
  LEVEL 사원 관리자 ISLEAF 
  ----- --------- ----- ----- 
  1 D C 0 
  2 C A 0 
  3 A 1
  ```

  ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_210.jpg)

  > 결과를 보면 표시 형태는 순방향 전개와 동일. 루트 및 레벨은 전개되는 방향에 따라 반대가 됨을 알 수 있다. 

- 계층형 질의에서 사용되는 함수

  ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_211.jpg)

  - 예제 코드 :

    ```SQL
    SELECT CONNECT_BY_ROOT 사원 루트사원, 
           SYS_CONNECT_BY_PATH(사원, '/') 경로, 
           사원, 
           관리자 
    FROM 사원 START WITH 관리자 IS NULL 
    CONNECT BY PRIOR 사원 = 관리자;
    ```

    ```SQL
    # 실행결과
    루트사원 경로 사원 관리자 
    ------- ------- ---- -----
    A /A A A /
    A/B B A A /
    A/C C A A /
    A/C/D D C A /
    A/C/E E C
    ```

    > START WITH를 통해 추출된 루트 데이터가 1건 이기 때문에 루트사원은 모두 A이다. 경로는 루트로부터 현재 데이터까지의 경로를 표시한다. 예를 들어, D의 경로는 A → C → D 이다.

## 집합 연산자 Set operators

> 조인 기법 외에도 집합연산자를 이용하여 테이블에서 데이터를 조회할 수 있다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_204.jpg)



#### 집합연산자의 규칙

- SELECT 에서 쓴 첫번째 열과, 두번 째의 열은 왼쪽부터 순서대로 **일대일 대응** 해야한다.
- 열 개수, 데이터 타입이 모두 일치해야한다.(하나라도 다르면 오류)
- SELECT 문의 연산은 위에서 아래로 수행



#### UNION 

각기 다른 두 개 이상의 SELECT 문을 실행한 결과를 하나로 묶어서 출력할 수 있습니다.(중복되는 행은 한번만 출력/합집합)

![img](https://thebook.io/img/006977/161.jpg)

- 예시코드

  ```sql
  SELECT department_id
  FROM   employees
  
  UNION
  
  SELECT department_id
  FROM   departments;
  ```

  > 두 SELECT 문 사이에 UNION 명령어를 사용하면서 중복없이 열을 합쳤다.

#### UNION ALL

 UNION 연산자와 동일한 기능을 하지만, 중복과 상관없이 모두 출력한다.

![img](https://thebook.io/img/006977/163.jpg)

- 예시코드

  ```sql
  SELECT department_id
  FROM   employees
  
  UNION  ALL
  
  SELECT department_id
  FROM   departments
  ORDER BY department_id;
  ```



#### INTERSECT 교집합

INTERSECT 연산자는 양쪽 SELECT문의 교집합 데이터만 출력한다.

![img](https://thebook.io/img/006977/164.jpg)

- 예시코드

  ```SQL
  SELECT department_id
  FROM   employees
  
  INTERSECT
  
  SELECT department_id
  FROM   departments
  ORDER BY department_id;
  ```



#### MINUS

MINUS 연산자는 첫번 째 SELECT문에서 뒤에 적은 SELECT문과 겹치는 데이터들을 빼고 출력한다.

![img](https://thebook.io/img/006977/165_2.jpg)

- 예시코드

  ```SQL
  SELECT department_id
  FROM   employees
  
  MINUS
  
  SELECT department_id
  FROM   departments
  ORDER BY department_id;
  ```

  



## Standard SQL

국내뿐만 아니라 전 세계적으로 많이 사용되고 있는 관계형 데이터베이스의 경우 오브젝트 개념을 포함한 여러 새로운 기능들이 꾸준히 개발되고 있다. 사용자 입장에서는 ANSI/ISO SQL의 새로운 기능들을 사용함으로써 보다 쉽게 데이터를 추출하거나 SQL 튜닝의 효과를 함께 얻을 수 있게 되었다. 



#### 종류

1. **일반 집합 연산자**

   *일반 집합 연산자를 현재 SQL과 비교하면,*

   - UNION 연산은 UNION 기능으로 `합집합`
   - INTERSECTION 연산은 INTERSECT 기능으로 `교집합`
   - DIFFERENCE 연산은 EXCEPT(Oracle은 MINUS) 기능으로 `차집합`
   - PRODUCT 연산은 CROSS JOIN 기능으로 구현 `곱집합` (조인조건이 없을 때 생길 수 있는 모든 데이터의 조합 / M*N건의 데이터 조합 발생)

   ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_200.jpg)

2. **순수 관계 연산자** (관계형 데이터베이스를 구현하기 위해 새롭게 만들어진 연산자)

   *순수 관계연산자를 현재 SQL과 비교하면,*

   -  SELECT 연산은 WHERE 절로 구현 (select연산과 select절의 의미는 다름)

   - PROJECT 연산은 SELECT 절의 칼럼 선택 기능으로 구현 됨

   - (NATURAL) JOIN 연산은 다양한 JOIN 기능으로 구현

   - DIVIDE 연산은 현재 사용되지 않는다.

     ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_201.jpg)

     

#### FROM 절의 JOIN 형태

ANSI/ISO SQL에서 표시하는 FROM 절의 JOIN 형태는 6가지 이다.

1. *INNER JOIN*
2. *NATURAL JOIN*
3. *USING 조건절*
4. *ON 조건절* 
5. *CROSS JOIN*
6. *OUTER JOIN*

공식적으로 SQL에서 규정한 JOIN의 문법은 WHERE 절을 사용하던 기존 방식과 차이가 있다.(내가 위에서 정리한 방식). 기존 방식에 **추가 선택기능으로** 테이블간의 JOIN 조건을 **FROM 절에서 명시**적으로 정의할 수 있게 되었다.

- INNER JOIN

  외부조인과 대비하여 내부조인 이라고 하며, 조인 조건에서 동일한 값이 있는 행만 반환했었다. **INNER JOIN** 은 그 동안 `WHERE` 절에 넣은 조인 조건을 `FROM` 조건에서 정의하겠다는 뜻이다. 이렇게 사용할 때는, `USING 조건절` 또는 `ON 조건절`을 사용해야한다.

  - 예시코드 :

    ```sql
    # WHERE 절 JOIN 조건 
    SELECT EMP.DEPTNO, EMPNO, ENAME, DNAME 
    FROM EMP, DEPT 
    WHERE EMP.DEPTNO = DEPT.DEPTNO;
    
    # 위 SQL과 아래 SQL은 같은 결과를 얻을 수 있다. 
    FROM 절 JOIN 조건 SELECT EMP.DEPTNO, EMPNO, ENAME, DNAME 
    FROM EMP INNER JOIN DEPT ON EMP.DEPTNO = DEPT.DEPTNO;
    
    # INNER는 JOIN의 디폴트 옵션으로 아래 SQL문과 같이 생략 가능하다.
    SELECT EMP.DEPTNO, EMPNO, ENAME, DNAME 
    FROM EMP JOIN DEPT ON EMP.DEPTNO = DEPT.DEPTNO;
    ```

- NATURAL JOIN

  이 조인 방법은 두 **테이블 간 동일한 이름을 갖는 모든 칼럼**을 동등 조인(`=`)한다. NATURAL JOIN이 명시되면, 추가로 USING 조건절, ON 조건절, WHERE 절에서 JOIN 조건을 **정의할 수 없다.** 그리고, SQL Server에서는 지원하지 않는 기능이다.

  - 예시코드 :

    ```SQL
    SELECT DEPTNO, EMPNO, ENAME, DNAME 
    FROM EMP NATURAL JOIN DEPT;
    ```

    > 위 SQL문은 조인할 칼럼을 지정하지 않았지만, 두개의 테이블에서 DEPTNO라 한 것이다.

  NATURAL JOIN은 조인되는 테이블의 데이터 성격과 칼럼명이 동일해야 하는 제약조건 있다.

- USING 조건절

  FROM 절의 USING 조건절을 이용하면 같은 이름을 가진 칼럼들 중에서 **원하는 칼럼에 대해서만 선택적으로** EQUI JOIN을 할 수가 있다. (NATURAL JOIN에서는 기본적으로는 모든 일치되는 칼럼들에 대해 JOIN이 된다.)

  ```SQL
  SELECT * 
  FROM DEPT JOIN DEPT_TEMP USING (DEPTNO);
  ```

  > 대신 FROM 절에서 조인할 때는 별칭이나 테이블 이름을 부여할 수 없다.

- ON 조건절

  이 방식은 칼럼명이 다르더라도 조인조건을 사용할 수 있는 장점이 있다.

  ```SQL
  SELECT E.EMPNO, E.ENAME, E.DEPTNO, D.DNAME 
  FROM EMP E JOIN DEPT D ON (E.DEPTNO = D.DEPTNO);
  ```

  > 사원 테이블과 부서 테이블에서 사원 번호와 사원 이름, 소속부서 코드, 소속부서 이름을 출력

  USING 조건절을 사용했을 때는 조인할 칼럼에 대해서 별칭과 테이블 명을 사용하면 에러가 뜨지만, ON 조건절을 사용할 때는 반대로 테이블 명과 칼럼을 정확하게 지정해줘야 한다.

  - WHERE 절과 혼용하는 ON조건절

    ```SQL
    SELECT E.ENAME, E.DEPTNO, D.DEPTNO, D.DNAME 
    FROM EMP E JOIN DEPT D ON (E.DEPTNO = D.DEPTNO) 
    WHERE E.DEPTNO = 30;
    ```

    > EMP와 DEPT 테이블을 FROM절에서 조인하면서 WHERE절에서 조건을 하나 더 추가했다.

  - 데이터 검증조건 추가

    ```SQL
    # ON조건절에서의 데이터 조건 추가
    SELECT E.ENAME, E.MGR, D.DEPTNO, D.DNAME 
    FROM EMP E JOIN DEPT D ON (E.DEPTNO = D.DEPTNO AND E.MGR = 7698); 
    
    # 위 SQL과 아래 SQL은 같은 결과를 얻을 수 있다. 
    SELECT E.ENAME, E.MGR, D.DEPTNO, D.DNAME 
    FROM EMP E JOIN DEPT D ON (E.DEPTNO = D.DEPTNO) 
    WHERE E.MGR = 7698;
    ```

    > ON조건절로도 검색조건을 추가할 수 있지만 WHERE절을 권장한다고 한다.

  - 다중 테이블 조인

    ```SQL
    # 사원과 DEPT 테이블의 소속 부서명, DEPT_TEMP 테이블의 바뀐 부서명 정보를 출력
    SELECT E.EMPNO, D.DEPTNO, D.DNAME, T.DNAME New_DNAME 
    FROM EMP E JOIN DEPT D ON (E.DEPTNO = D.DEPTNO) JOIN DEPT_TEMP T ON (E.DEPTNO = T.DEPTNO); 
    
    위 SQL은 고전적인 방식인 WHERE 절의 INNER JOIN으로 구현할 수도 있다. 
    SELECT E.EMPNO, D.DEPTNO, D.DNAME, T.DNAME New_DNAME 
    FROM EMP E, DEPT D, DEPT_TEMP T
    WHERE E.DEPTNO = D.DEPTNO 
    AND E.DEPTNO = T.DEPTNO;
    ```

    ```SQL
    # GK 포지션의 선수별 연고지명, 팀명, 구장명을 출력
    SELECT P.PLAYER_NAME 선수명, P.POSITION 포지션, 
           T.REGION_NAME 연고지명, T.TEAM_NAME 팀명, 
           S.STADIUM_NAME 구장명 
    FROM PLAYER P 
    JOIN TEAM T ON P.TEAM_ID = T.TEAM_ID 
    JOIN STADIUM S ON T.STADIUM_ID = S.STADIUM_ID 
    WHERE P.POSITION = 'GK' 
    ORDER BY 선수명; 
    
    # 위 SQL은 고전적인 방식인 WHERE 절의 INNER JOIN으로 구현할 수도 있다. 
    SELECT P.PLAYER_NAME 선수명, P.POSITION 포지션, T.REGION_NAME 연고지명, T.TEAM_NAME 팀명, S.STADIUM_NAME 구장명 
    FROM PLAYER P, TEAM T, STADIUM S 
    WHERE P.TEAM_ID = T.TEAM_ID 
    AND T.STADIUM_ID = S.STADIUM_ID 
    AND P.POSITION = 'GK' 
    ORDER BY 선수명;
    ```

    ```SQL
    # 홈팀이 3점 이상 차이로 승리한 경기의 경기장 이름, 경기 일정, 홈팀 이름과 원정팀   이름 정보를 출력
    SELECT ST.STADIUM_NAME, SC.STADIUM_ID, 
           SCHE_DATE, HT.TEAM_NAME, AT.TEAM_NAME, 
           HOME_SCORE, AWAY_SCORE 
    FROM SCHEDULE SC 
    JOIN STADIUM ST ON SC.STADIUM_ID = ST.STADIUM_ID 
    JOIN TEAM HT ON SC.HOMETEAM_ID = HT.TEAM_ID 
    JOIN TEAM AT ON SC.AWAYTEAM_ID = AT.TEAM_ID 
    WHERE HOME_SCORE > = AWAY_SCORE +3; 
    
    # 위 SQL은 고전적인 방식인 WHERE 절의 INNER JOIN으로 구현할 수도 있다. 
    SELECT ST.STADIUM_NAME, SC.STADIUM_ID, 
           SCHE_DATE, HT.TEAM_NAME, AT.TEAM_NAME, 
           HOME_SCORE, AWAY_SCORE 
    FROM SCHEDULE SC, STADIUM ST, TEAM HT, TEAM AT 
    WHERE HOME_SCORE> = AWAY_SCORE +3 
    AND SC.STADIUM_ID = ST.STADIUM_ID 
    AND SC.HOMETEAM_ID = HT.TEAM_ID 
    AND SC.AWAYTEAM_ID = AT.TEAM_ID;
    ```

    > FROM 절에 4개의 테이블이 JOIN에 참여하였으며, HOME TEAM과 AWAY TEAM의 팀 이름을 구하기 위해 TEAM 테이블을 HT와 AT 두 개의 ALIAS로 구분하였다.

- CROSS JOIN

  테이블 간 JOIN 조건이 없는 경우 생길 수 있는 모든 데이터의 조합을 말한다.  결과는 양쪽 집합의 **M*N 건의 데이터 조합**이 발생한다. 

  ```SQL
  # 이 코드로 56개의 행이 선택되는 결과를 볼 수 있다.
  SELECT ENAME, DNAME 
  FROM EMP CROSS JOIN DEPT 
  ORDER BY ENAME;
  ```

  > 위 56건의 데이터는 EMP 14건 * DEPT 4건의 데이터 조합 건수이다.

- OUTER JOIN

  외부조인이라고 하는 이 조건은, 동일한 값이 없는 행도 반환할 때 사용할 수 있다.

  1. LEFT OUTER JOIN

     예제 : STADIUM에 등록된 운동장 중에는 홈팀이 없는 경기장도 있다. STADIUM과 TEAM을 JOIN 하되 홈팀이 없는 경기장의 정보도 같이 출력하도록 한다.

     ```SQL
     SELECT STADIUM_NAME, STADIUM.STADIUM_ID, 
            SEAT_COUNT, HOMETEAM_ID, TEAM_NAME 
     FROM STADIUM LEFT OUTER JOIN TEAM ON STADIUM.HOMETEAM_ID = TEAM.TEAM_ID ORDER BY HOMETEAM_ID; 
     
     # OUTER는 생략 가능한 키워드이므로 아래 SQL은 같은 결과를 얻을 수 있다. 
     SELECT STADIUM_NAME, STADIUM.STADIUM_ID, 
            SEAT_COUNT, HOMETEAM_ID, TEAM_NAME 
     FROM STADIUM LEFT JOIN TEAM ON STADIUM.HOMETEAM_ID = TEAM.TEAM_ID 
     ORDER BY HOMETEAM_ID;
     ```

     > 조인 할 때, 좌측에 먼저 쓴 테이블을 먼저 읽고 우측 테이블에서 조인 대상 데이터를 추가로 읽어온다.

  2. RIGHT OUTER JOIN

     예제 : DEPT에 등록된 부서 중에는 사원이 없는 부서도 있다. DEPT와 EMP를 조인하되 사원이 없는 부서 정보도 같이 출력하도록 한다.

     ```SQL
     SELECT E.ENAME, D.DEPTNO, D.DNAME 
     FROM EMP E RIGHT OUTER JOIN DEPT D ON E.DEPTNO = D.DEPTNO; 
     
     # OUTER는 생략 가능한 키워드이므로 아래 SQL은 같은 결과를 얻을 수 있다. 
     SELECT E.ENAME, D.DEPTNO, D.DNAME, D.LOC 
     FROM EMP E RIGHT JOIN DEPT D ON E.DEPTNO = D.DEPTNO;
     ```

     > 조인 수행시 LEFT JOIN과 반대로 우측 테이블이 기준이 되어 결과를 생성한다. 

  3. FULL OUTER JOIN

     예제 : DEPTNO 기준으로 DEPT와 DEPT_TEMP 데이터를 FULL OUTER JOIN으로 출력한다. 예제에 사용된 UNION 중복 데이터는 제거됨

     ```SQL
     SELECT * 
     FROM DEPT FULL OUTER JOIN DEPT_TEMP ON DEPT.DEPTNO = DEPT_TEMP.DEPTNO; 
     
     # OUTER는 생략 가능한 키워드이므로 아래 SQL은 같은 결과를 얻을 수 있다. 
     SELECT * 
     FROM DEPT FULL JOIN DEPT_TEMP ON DEPT.DEPTNO = DEPT_TEMP.DEPTNO; 
     
     # 위 SQL과 아래 SQL은 같은 결과를 얻을 수 있다. 
     SELECT L.DEPTNO, L.DNAME, L.LOC, R.DEPTNO, R.DNAME, R.LOC 
     FROM DEPT L LEFT OUTER JOIN DEPT_TEMP R ON L.DEPTNO = R.DEPTNO 
     
     UNION 
     
     SELECT L.DEPTNO, L.DNAME, L.LOC, R.DEPTNO, R.DNAME, R.LOC 
     FROM DEPT L RIGHT OUTER JOIN DEPT_TEMP R ON L.DEPTNO = R.DEPTNO;
     ```

     > 즉, RIGHT OUTER JOIN과 LEFT OUTER JOIN의 결과를 합집합으로 처리한 결과와 동일하다.

  