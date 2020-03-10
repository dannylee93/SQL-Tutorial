# 서브쿼리 Sub - Query

> 서브쿼리(Subquery)란 하나의 SQL문안에 포함되어 있는 또 다른 SQL문을 말한다. 즉 SELECT문 안에 다시 SELECT 문이 기술된 형태의 쿼리다.



![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_214.jpg)

복잡한 논리를 가진 SQL문에서는 거의 필수로 사용된다. 

#### 서브쿼리의 기본 규칙

- 서브쿼리는 `( )` 괄호를 묶어서 사용한다.

- 메인쿼리와 서브쿼리를 사용하기 위해서는 단일 행 연산자 / 다중 행 연산자를 사용한다.

- 이 연결 형태는 연산자를 뭘 쓰느냐에 따르 의미가 달라진다.

- 메인쿼리는 가장 왼쪽에 기술하고, 서브쿼리는 오른쪽에 기술한다.

- 서브쿼리에서 메인쿼리의 순서로 SELECT문이 기술된다.

- 서브쿼리는 계속 중첩해서 사용할 수 있다.

- 서브쿼리가 SQL문에서 사용이 가능한 곳은 다음과 같다.

  ```SQL
  - SELECT 절 
  - FROM 절 
  - WHERE 절 
  - HAVING 절 
  - ORDER BY 절 
  - INSERT문의 VALUES 절 
  - UPDATE문의 SET 절
  ```



## 단일 행 서브쿼리

> 단일 행 서브쿼리는 서브쿼리의 SELECT문에서 얻은 한 개 행의 결과값을 메인 쿼리로 전달하는 서브쿼리이다. 이 때 메인과 서브를 연결하는 연산자로 <단일 행 연산자>를 사용한다.



#### 예제 코드 

employees 테이블의 last_name이 'De Haan'인 직원과 salary가 동일한 직원이 누가 있는지 단일 행 서브쿼리로 출력해보자

```sql
SELECT *
FROM employees AS A
WHERE A.salary = (
                  SELECT salary
                  FROM employees
                  WHERE last_name = 'De Haan'
                  );
```

> 위의 결과를 실행해보면 서브쿼리에서 'De Haan'의 결과값이 17000 이였으며, 이 값을 가지고 메인쿼리에서 salary가 동일한 모든 사람이 출력 되었다.

하지만 이름을 바꿔 'Taylor'로 실행해봤을 때는 오류가 발생한다. 왜냐면 Taylor라는 이름을 가진 사람이 2명으로 다중 행이 되기 때문에 연산자 오류가 발생하기 때문이다.



## 다중 행 서브쿼리

> 사용방법은 기본적으로 단일 행 서브쿼리와 동일하며, <다중 행 연산자> 를 사용하는 점이 다르다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_219.jpg)



#### 예제 코드

employees 테이블에서 department_id별로 가장 낮은 salary가 얼마인지 찾아보고, 찾아낸 salary에 해당하는 직원이 누구인지 다중 행 서브쿼리를 이용해 찾아보자

```sql
SELECT *
FROM employees AS A IN(
                       SELECT MIN(salary) 최저급여
                       FROM employees
                       GROUP BY department_id)
ORDER BY A.salary DESC;
```

> 다중 행 연산자 IN을 사용하여 서브쿼리의 부서별 최저급여 정보가 메인쿼리로 들어오면서 해당 하는 모든 사람을 출력 했다.



## 다중 열(칼럼) 서브쿼리

> 다중 열 서브쿼리는 WHERE 조건식에서 비교되는 열이 여러 개일 때 사용하는 서브쿼리이다.



#### 예제코드

employees 테이블에서 job_id 별로 가장 낮은 salary가 얼마인지 찾아보고, 찾아낸 job_id별 salary에 해당하는 직원이 누구인지 다중 열 서브쿼리를 이용해 찾아보세요.

```SQL
SELECT * 
FROM employees AS A
WHERE A.job_id A.salary IN(
                           SELECT job_id, MIN(salary) 최저급여
                           FROM employees
                           GROUP BY job_id)
ORDER BY A.salary DESC;
```





## 연관 서브쿼리

> 연관 서브쿼리(Correlated Subquery)는 서브쿼리 내에 메인쿼리 칼럼이 사용된 서브쿼리이다.



#### 예제코드

 ```sql
SELECT T.TEAM_NAME 팀명, 
       M.PLAYER_NAME 선수명, 
       M.POSITION 포지션, 
       M.BACK_NO 백넘버, 
       M.HEIGHT 키 
FROM PLAYER M, TEAM T 
WHERE M.TEAM_ID = T.TEAM_ID AND M.HEIGHT < ( 
                                             SELECT AVG(S.HEIGHT) 
                                             FROM PLAYER S 
                                             WHERE S.TEAM_ID = M.TEAM_ID 
                                             AND S.HEIGHT IS NOT NULL
                                             GROUP BY S.TEAM_ID 
                                            )
ORDER BY 선수명;
 ```

> 선수 자신이 속한 팀의 평균 키보다 작은 선수들의 정보를 출력하는 SQL문을 연관 서브쿼리를 이용해서 작성해 보면 다음과 같다.

예를 들어, 

가비 선수는 삼성블루윙즈팀 소속이므로 삼성블루윙즈팀 소속의 *평균키*를 구하고 그 평균키와 가비 선수의 키를 *비교하여 적을 경우에* 선수에 대한 정보를 출력한다. 

만약, 평균키 보다 선수의 키가 크거나 같으면 조건에 맞지 않기 때문에 해당 데이터는 출력되지 않는다. 이와 같은 작업을 메인쿼리에 존재하는 **모든 행에 대해서 반복 수행한다**.



## 기타 서브쿼리

1. SELECT 절에서 서브쿼리 사용하기

   ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_220.jpg)

   > 상단의 그림은 2개의 SQL문 조합이다. 선수정보인 메인쿼리와 해당 소속 선수의 팀별 평균키를 알아내는 서브쿼리이다.

   SELECT 절에서 사용하는 서브쿼리를 **스칼라 서브쿼리(Scalar Subquery)**라고 한다.

   - 예제코드 :

     ```sql
     SELECT PLAYER_NAME 선수명, HEIGHT 키, (
                                           SELECT AVG(HEIGHT) 
                                           FROM PLAYER X 
                                           WHERE X.TEAM_ID = P.TEAM_ID
                                           ) AS 팀평균키 
     FROM PLAYER P
     ```

2. FROM 절에서 서브쿼리 사용하기

   FROM절에서 서브쿼리를 사용하는 것을 `인라인 뷰(Inline View)` 라고 한다. 원래 FROM절에는 테이블 명이 온다. 근데 서브쿼리가 FROM절에서 사용되면 **서브쿼리의 결과가 마치 실행 시에 동적으로 생성된 테이블인 것처럼 사용**할 수 있다. 

   그래서 일반적인 뷰를 **정적 뷰(Static View)** 라고도 하며 인라인 뷰를 **동적 뷰(Dynamic View)** 라고도 부른다.

   - 예제코드 :

     ```sql
     SELECT T.TEAM_NAME 팀명, P.PLAYER_NAME 선수명, P.BACK_NO 백넘버 
     FROM (
           SELECT TEAM_ID, PLAYER_NAME, BACK_NO 
           FROM PLAYER 
           WHERE POSITION = 'MF'
           ) P, 
           TEAM T       
     WHERE P.TEAM_ID = T.TEAM_ID 
     ORDER BY 선수명;
     ```

     >  인라인 뷰의 칼럼은 SQL문 자유롭게 참조할 수 있다. K-리그 선수들 중에서 포지션이 미드필더(MF)인 선수들의 소속팀명 및 선수 정보를 출력하고자 한다.

     ```sql
     # TOP N
     SELECT PLAYER_NAME 선수명, POSITION 포지션, BACK_NO 백넘버, HEIGHT 키 
     FROM (
           SELECT PLAYER_NAME, POSITION, BACK_NO, HEIGHT 
           FROM PLAYER 
           WHERE HEIGHT IS NOT NULL 
           ORDER BY HEIGHT DESC
           )
     WHERE ROWNUM <= 5;
     ```

     > 인라인 뷰에서는 ORDER BY절을 사용할 수 있다. 인라인 뷰에 먼저 정렬을 수행하고 정렬된 결과 중에서 일부 데이터를 추출하는 것을 TOP-N 쿼리라고 한다. 

3. HAVING 절에서 서브쿼리 사용하기

   HAVING 절은 그룹함수와 함께 사용될 때 그룹핑 된 결과에 대해 부가적인 조건을 주기 위해 사용한다.

   ```SQL
   SELECT P.TEAM_ID 팀코드, T.TEAM_NAME 팀명, AVG(P.HEIGHT) 평균키 
   FROM PLAYER P, TEAM T 
   WHERE P.TEAM_ID = T.TEAM_ID 
   GROUP BY P.TEAM_ID, T.TEAM_NAME 
   HAVING AVG(P.HEIGHT) < (
                           SELECT AVG(HEIGHT) 
                           FROM PLAYER 
                           WHERE TEAM_ID ='K02'
                           )
   ```

   > 평균키가 삼성 블루윙즈팀의 평균키보다 작은 팀의 이름과 해당 팀의 평균키를 구하는 SQL문

4. UPDATE문의 SET 절에서 사용하기

   현재 테이블에 없는 새로운 칼럼을 추가할 때 사용.

   ```SQL
   UPDATE TEAM A 
   SET A.STADIUM_NAME = (
                         SELECT X.STADIUM_NAME 
                         FROM STADIUM X 
                         WHERE X.STADIUM_ID = A.STADIUM_ID
                         )
   ;
   ```

   >  TEAM 테이블에 추가된 STADIUM_NAME의 값을 STADIUM 테이블을 이용하여 변경하고자 할 때 다음과 같이 SQL문

5. INSERT문의 VALUES 절에서 사용하기

   테이블에 새로운 선수(행 데이터) 삽입할 때 사용한다.

   ```SQL
   # 3개 열에 3개 데이터 삽입
   
   INSERT INTO PLAYER(PLAYER_ID, PLAYER_NAME, TEAM_ID) 
   VALUES(
          (SELECT TO_CHAR(MAX(TO_NUMBER(PLAYER_ID))+1) FROM PLAYER), 
           '홍길동', 
           'K06'
          )
   ;
   ```

   

## 뷰 View

> 테이블은 실제 데이터를 가지고 있지만, 뷰 는 실제 데이터를 가지고 있지 않다. 하지만 테이블이 수행하는 역할을 수행하기 때문에 가상 테이블(Virtual Table)이라고도 한다.



![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_221.jpg)

#### 예제코드

- 뷰는 `CREATE VIEW` 문으로 생성

  ```SQL
  CREATE VIEW V_PLAYER_TEAM AS 
  SELECT P.PLAYER_NAME, P.POSITION, P.BACK_NO, P.TEAM_ID, T.TEAM_NAME 
  FROM PLAYER P, TEAM T 
  WHERE P.TEAM_ID = T.TEAM_ID;
  ```

  > 이 뷰 작성문은 선수정보와 해당 선수가 속한 팀명을 같이 추출하며 뷰 명칭은 'V_PLAYER_TEAM' 이다.

- 뷰는 테이블 뿐만 아니라 이미 존재하는 뷰를 참조해서도 생성 가능

  ```SQL
  CREATE VIEW V_PLAYER_TEAM_FILTER AS 
  SELECT PLAYER_NAME, POSITION, BACK_NO, TEAM_NAME 
  FROM V_PLAYER_TEAM 
  WHERE POSITION IN ('GK', 'MF');
  ```

  > V_PLAYER_TEAM_FILTER 뷰는 이미 앞에서 생성했던 V_PLAYER_TEAM 뷰를 기반으로 해서 생성된 뷰다. 이번 코드는 이미 생성한 뷰에서도 특정 포지션만 추출하는 뷰 이다.

- 이제 생성해본 뷰를 사용해보자

  ```SQL
  SELECT PLAYER_NAME, POSITION, BACK_NO, TEAM_ID, TEAM_NAME 
  FROM V_PLAYER_TEAM 
  WHERE PLAYER_NAME LIKE '황%'
  ```

  > 해당 뷰의 이름을 FROM에서 사용하면 된다.