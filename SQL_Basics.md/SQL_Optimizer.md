# SQL - Optimizer

> 옵티마이저는 사용자가 질의한 SQL문에 대해 최적의 실행 방법을 결정하는 역할을 수행한다. 이 최적의 실행 방법을 실행계획(Execution Plan)이라고 한다. 



 ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_239.jpg)

> 최적의 실행 방법을 결정하는 방식에 따라 [그림 Ⅱ-3-1]과 같이 규칙기반 옵티마이저(RBO, Rule Based Optimizer)와 비용기반 옵티마이저(CBO, Cost Based Optimizer)로 구분할 수 있다.



## Optimizer

> 정해진 우선순위 또는 통계 정보를 이용하여 SELECT 문의 질의 성능이 최적화될 수 있도록 실행계획을 수립한다.



관계형 데이터베이스는 옵티마이저가 결정한 실행 방법대로 실행 엔진이 데이터를 처리하여 결과 데이터를 사용자에게 전달할 뿐이다. 옵티마이저가 선택한 실행 방법의 적절성 여부는 질의의 수행 속도에 가장 큰 영향 미치게 된다.



#### 옵티마이저의 작동 원리

<p align='center'><img src = "https://thebook.io/img/006977/227.jpg"/></p>

- `CBO` : Cost Based Optimizer 

- `SBO` : Rule Based Optimizer

  |   구분   |             CBO              |               SBO                |
  | :------: | :--------------------------: | :------------------------------: |
  |   개념   | 사전에 정의된 규칙 기반 계획 |   최소비용 계산, 실행계획 수립   |
  |   기준   |        실행 우선순위         |           액세스 비용            |
  |   성능   |   사용자의 SQL 작성 숙련도   |       옵티마이저 예측 성능       |
  |   특징   |  실행 계획의 예측이 용이함   |     저장된 통계 정보의 활용      |
  | 고려사항 | 저효율, 사용자의 규칙 이해도 | 예측 복잡, 비용 산출 공식 정확성 |

  

### (1) 규칙 기반 옵티마이저

규칙 기반 옵티마이저에서 `규칙`이란 **우선 순위** 를 말한다. 이 규칙을 가지고 실행계획을 생성한다.

규칙기반 옵티마이저는 **우선 순위가 높은 규칙이 적은 일량**으로 해당 작업을 수행하는 방법이라고 판단하는 것이다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_240.jpg)

> 위의 표는 Oracle의 규칙기반 옵티마이저의 15가지 규칙이다. 순위의 숫자가 낮을수록 높은 우선 순위이다.

1. `Single row by rowid` : 

   ROW-ID를 통해서 테이블에서 **하나의 행을 액세스**하는 방식이다. ROW-ID는 행이 포함된 데이터 파일, 블록 등의 정보를 가지고 있기 때문에 다른 정보를 참조하지 않고도 바로 원하는 행을 액세스할 수 있다. 하나의 행을 액세스하는 가장 빠른 방법이다.

4. `Single row by unique or primary key` : 

   **유일 인덱스(Unique Index)**를 통해서 **하나의 행을 액세스**하는 방식이다. 이 방식은 인덱스를 먼저 액세스하고 인덱스에 존재하는 ROWID를 추출하여 테이블의 행을 액세스한다.

8. `Composite index` : 

   **복합 인덱스**에 **동등(‘=’ 연산자) 조건으로 검색**하는 경우이다. 

   예를 들어, 만약 A+B 칼럼으로 복합 인덱스가 생성되어 있고, 조건절에서 WHERE A=10 AND B=1 형태로 검색하는 방식이다. 복합 인덱스 사이의 우선 순위 규칙은 다음과 같다. 인덱스 구성 칼럼의 개수가 더 많고 해당 인덱스의 모든 구성 칼럼에 대해 ‘=’로 값이 주어질 수록 우선순위가 더 높다. 예를 들어, A+B로 구성된 인덱스와 A+B+C로 구성된 인덱스가 각각 존재하고 조건절에서 A, B, C 칼럼 모두에 대해 ‘=’로 값이 주어진다면 A+B+C 인덱스가 우선 순위가 높다. 만약 조건절에서 A, B 칼럼에만 ‘=’로 값이 주어진다면 A+B는 인덱스의 모든 구성 칼럼에 대해 값이 주어지고 A+B+C 인덱스 입장에서는 인덱스의 일부 칼럼에 대해서만 값이 주어졌기 때문에 A+B 인덱스가 우선 순위가 높게 된다.

9. `Single column index` : 

   **단일 칼럼 인덱스**에 `=` 조건으로 검색하는 경우이다. 만약 A 칼럼에 단일 칼럼 인덱스가 생성되어 있고, 조건절에서 A=10 형태로 검색하는 방식이다.

10. `Bounded range search on indexed columns` : 

    인덱스가 생성되어 있는 칼럼에 **양쪽 범위를 한정하는 형태로 검색**하는 방식이다. 이러한 연산자에는 `BETWEEN`, `LIKE` 등이 있다. 만약 A 칼럼에 인덱스가 생성되어 있고, A BETWEEN ‘10’ AND ‘20’ 또는 A LIKE '1%' 형태로 검색하는 방식이다.

11. `Unbounded range search on indexed columns` : 

    인덱스가 생성되어 있는 칼럼에 **한쪽 범위만 한정하는 형태로 검색**하는 방식이다. 이러한 연산자에는 `>, >=, <, <=` 등이 있다. 만약 A 칼럼에 인덱스가 생성되어 있고, `A > '10'` 또는 `A < '20'` 형태로 검색하는 방식이다.

15. `Full table scan` : 

    **전체 테이블을 액세스**하면서 조건절에 **주어진 조건**을 만족하는 행만을 결과로 추출한다.

#### 주의사항

인덱스를 이용한 액세스 방식이 전체 테이블 액세스 방식보다 우선순위가 높다. 따라서 SQL을 사용할 때 쓸 수 있는 인덱스가 존재하면 전체 테이블 액세스 방식보다 항상 인덱스를 사용하는 실행계획을 생성한다.

#### 최적화 과정

```sql
SELECT ENAME 
FROM EMP
WHERE JOB = 'SALESMAN' AND SAL BETWEEN 3000 AND 6000 

INDEX --------------------------------- EMP_JOB : JOB EMP_SAL : SAL PK_EMP : EMPNO (UNIQUE)
```

- 조건절에서 JOB 칼럼의 조건은 ‘=’, SAL 칼럼의 조건은 ‘BETWEEN’으로
- 우선 순위 규칙에 따라 JOB 조건은 규칙 9의 단일 칼럼 인덱스를 만족
- SAL 조건은 규칙 10의 인덱스상의 양쪽 한정 검색을 만족한다.
- 따라서 우선 순위가 높은 EMP_JOB 인덱스를 이용해서 조건을 만족하는 행에 대해 EMP 테이블을 액세스하는 방식을 선택할 것이다.

```
Execution Plan ------------------------------------------------------------ SELECT STATEMENT Optimizer=CHOOSE TABLE ACCESS (BY INDEX ROWID) OF 'EMP' INDEX (RANGE SCAN) OF 'EMP_JOB' (NON-UNIQUE)
```

> 규칙기반 옵티마이저가 생성한 실행계획이다.



### (2) 비용 기반 옵티마이저

단순한 몇 개의 규칙만으로 현실의 모든 사항을 정확히 예측할 수는 없다. 비용기반 옵티마이저는 이러한 규칙기반 옵티마이저의 단점을 극복하기 위해서 출현하였다.

비용기반 옵티마이저는 SQL문을 **처리하는데 필요한 비용이 가장 적은** 실행계획을 선택하는 방식이다.

여기서 비용이란 SQL문을 처리하기 위해 예상되는 **소요시간** 또는 **자원 사용량**을 의미한다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_241.jpg)

> 비용기반 옵티마이저는 질의 변환기, 대안 계획 생성기, 비용 예측기 등의 모듈로 구성되어 있다.

- `대안 계획 생성기` : 동일한 결과를 생성하는 다양한 대안 계획을 생성하는 모듈.

  대안 계획은 연산의 **적용 순서 변경**, **연산 방법 변경**, **조인 순서 변경** 등을 통해서 생성된다. 즉, 대안 계획을 세우는 시간이 너무 많아지지 않는 선에서 다양한 방법을 구사하자는 말

- `비용 예측기` : 대안 계획 생성기에 의해서 생성된 대안 계획의 비용을 예측하는 모듈.

  - 대안 계획의 정확한 비용을 예측하기 위해서 연산의 중간 집합의 크기 및 결과 집합의 크기, 분포도 등의 예측이 정확해야 한다.

- 비용기반 옵티마이저는 인덱스를 사용하는 비용이 전체 테이블 스캔 비용보다 크다고 판단되면 전체 테이블 스캔을 수행하는 방법으로 실행계획을 생성할 수도 있다.



### 옵티마이저의 실행 계획

**실행계획(Execution Plan)**이란 SQL에서 요구한 사항을 처리하기 위한 절차와 방법을 의미한다. 

실행계획을 생성한다는 것은 SQL을 **어떤 순서**로 **어떻게 실행**할 지를 결정하는 작업이다.

 옵티마이저는 최적의 실행계획을 생성해 준다.

 ![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_242.jpg)

> 실행계획을 구성하는 요소에는 조인 순서(Join Order), 조인 기법(Join Method), 액세스 기법(Access Method), 최적화 정보(Optimization Information), 연산(Operation) 등이 있다.



### SQL 처리 흐름도

SQL **처리 흐름도(Access Flow Diagram)**란 SQL의 내부적인 처리 절차를 시각적으로 표현한 도표이다. (실행계획을 시각화 한 것이다.)

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_243.jpg)

> TAB1에 대한 액세스는 스캔(Scan) 방식이고 조인시도 및 I01_TAB2 인덱스를 통한 TAB2 액세스는 랜덤(Random) 방식이다.

- 어떤 테이블을 먼저 읽었는지 조인 순서를 볼 수 있다.
- 테이블을 읽기 위해 인덱스 스캔을 했는지 or 테이블 전체 스캔을 했는지(액세스 기법)와 조인 기법을 표현할 수 있다.
-  [그림 Ⅱ-3-5]에서 조인 순서는 TAB1 → TAB2이다.
- TAB1을 **Outer Table** 또는 **Driving Table**이라고 하고, TAB2를 **Inner Table** 또는 **Lookup Table** 이라고 한다.
- 성능적인 관점을 살펴보기 위해서 SQL 처리 흐름도에 일량을 함께 표시할 수 있다. [그림 Ⅱ-3-5]에서 건수(액세스 건수, 조인 시도 건수, 테이블 액세스 건수, 성공 건수)라고 표시된 곳에 SQL 처리를 위해 작업한 건수 또는 처리 결과 건수 등의 일량을 함께 표시할 수 있다. 



## Index

> 인덱스(Index)는 데이터를 찾기 위한 '색인'으로 데이터의 주소록이라고 부를 수 있다. 데이터를 빠르고 효율적으로 조회하기 위해 사용한다.



#### 데이터의 조회 단계(버퍼 캐시에 주목)

<p align='center'><img src = "https://thebook.io/img/006977/230.jpg"/></p>

- `버퍼 캐시` : 자주 사용되는 테이블의 데이터 정보가 저장되어 있는 일종의 '가속장치' 이다.

- 버퍼캐시에 조회하는 데이터가 없다면 우리는 3번과 같이 **데이터베이스** 안에 있는 데이터를 찾아내야 한다.

- 모든 데이터를 버퍼 캐시에 저장할 수는 없다.(관리 효율성 & 비용 문제)

- 내가 원하는 데이터가 어디 존재하는 지 알려주는 **색인** 이 있다면 훨씬 쉽게 데이터를 검색해서 반환할 수 있다.

- 이 색인 값만 별도로 만들어서 관리하는 기법이 **인덱스** (== `Row_id`)

  ![](data:image/jpeg;base64,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)



#### 주의할 점

- 분석시스템(OLAP) & 운영시스템(OLTP)에 따라 인덱스 유형이 달라진다. 
- 인덱스가 지나치게 많으면 과부하 발생

#### 추천 상황

- 열이 WHERE 절의 join 조건으로 자주 사용될 때
- 열에 다양한 값이 있거나 null 값이 많을 때
- *그 반대 상황은 사용하지 않는 편이 낫다.*





### (1) 트리 기반 인덱스

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_244.jpg)

> DBMS에서 가장 일반적인 인덱스는 B-트리 인덱스이다.

- B-트리 인덱스는 브랜치 블록(Branch Block)과 리프 블록(Leaf Block)으로 구성된다.
- 가장 상위에서 있는 블록을 루트 블록(Root Block)이라고 한다.
- 브랜치 블록은 분기를 목적으로 하는 블록이다.
- 리프 블록은 트리의 가장 아래 단계에 존재한다.
- 리프 블록은 인덱스를 구성하는 칼럼의 데이터와 해당 데이터를 가지고 있는 행의 위치를 가리키는 레코드 식별자(RID, Record Identifier/Rowid)로 구성되어 있다. 리프 블록은 양방향 링크(Double Link)를 가지고 있다.



#### 인덱스에서 원하는 값 찾는 과정

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_245.jpg)

1. 브랜치 블록의 가장 왼쪽 값이 찾고자 하는 값보다 작거나 같으면 왼쪽 포인터로 이동
2. 찾고자 하는 값이 브랜치 블록의 값 사이에 존재하면 가운데 포인터로 이동
3. 오른쪽에 있는 값보다 크면 오른쪽 포인터로 이동



### (2) SQL - Server의 클러스터형 인덱스

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_246.jpg)

> Employee ID, Last Name, First Name, Hire Date로 구성된 Employees 테이블에 대해 Employee ID에 기반한 클러스터형 인덱스를 생성한 모습이다. B-트리 구조를 편의상, 삼각형 모양을 왼쪽으로 90도 돌려서 나타냈다.



#### 중요한 두가지 특징

1.  인덱스의 리프 페이지가 곧 데이터 페이지다. 따라서 테이블 탐색에 필요한 레코드 식별자가 리프 페이지에 없다.(인덱스 키 칼럼과 나머지 칼럼을 리프 페이지에 같이 저장하기 때문에 테이블을 랜덤 액세스할 필요가 없다) 클러스터형 인덱스의 리프 페이지를 탐색하면 해당 테이블의 모든 칼럼 값을 곧바로 얻을 수 있다.

   (흔히 클러스터 인덱스를 사전에 비유한다. 영한사전은 알파벳 순으로 정렬 되어있고, 각 단어 바로 옆에 한글 의미가 있다. )

2. 리프 페이지의 모든 로우(=데이터)는 인덱스 키 칼럼 순으로 물리적으로 정렬되어 저장된다. 테이블 로우는 물리적으로 한 가지 순서로만 정렬될 수 있다. 그러므로 클러스터형 인덱스는 테이블당 한 개만 생성할 수 있다.

   (전화번호부 한 권을 상호와 인명으로 동시에 정렬할 수 없는 것과 마찬가지다.)



### (3) 전체 테이블 스캔 & 인덱스 스캔



#### 전체 테이블 스캔

전체 테이블 스캔 방식으로 데이터를 검색한다는 것은 테이블에 존재하는 모든 데이터를 읽어 가면서 조건에 맞으면 결과로서 추출하고 조건에 맞지 않으면 버리는 방식으로 검색한다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_247.jpg)

#### 옵티마이저가 연산으로 전체 테이블 스캔을 선택하는 이유

1.  SQL문에 조건이 존재하지 않는 경우

   SQL문에 조건이 존재하지 않는다는 것은 테이블에 존재하는 모든 데이터가 답이 된다는 것이다. 그렇기 때문에 테이블의 모든 블록을 읽으면서 무조건 결과로서 반환하면 된다.

2.  SQL문의 주어진 조건에 사용 가능한 인덱스가 존재하는 않는 경우

   사용 가능한 인덱스가 존재하지 않는다면 데이터를 액세스할 수 있는 방법은 테이블의 모든 데이터를 읽으면서 주어진 조건을 만족하는지를 검사하는 방법뿐이다. 또한 주어진 조건에 사용 가능한 인덱스는 존재하나 함수를 사용하여 인덱스 칼럼을 변형한 경우에도 인덱스를 사용할 수 없다.

3. 옵티마이저의 취사 선택

   조건을 만족하는 데이터가 많은 경우, 결과를 추출하기 위해서 테이블의 대부분의 블록을 액세스해야 한다고 옵티마이저가 판단하면 조건에 사용 가능한 인덱스가 존재해도 전체 테이블 스캔 방식으로 읽을 수 있다.

4. 그 밖의 경우

   병렬처리 방식으로 처리하는 경우 또는 전체 테이블 스캔 방식의 힌트를 사용한 경우에 전체 테이블 스캔 방식으로 데이터를 읽을 수 있다.

#### 인덱스 스캔

인덱스 스캔은 인덱스를 구성하는 칼럼의 값을 기반으로 데이터를 추출하는 액세스 기법이다.

인덱스의 리프 블록은 인덱스 구성하는 **칼럼**과 **레코드 식별자**로 구성되어 있다. 따라서 검색을 위해 인덱스의 리프 블록을 읽으면 인덱스 구성 칼럼의 값과 테이블의 레코드 식별자를 알 수 있다.

인덱스는 인덱스 구성 칼럼의 순서로 정렬되어 있다. 인덱스의 구성 칼럼이 A+B라면 먼저 칼럼 A로 정렬되고 칼럼 A의 값이 동일할 경우에는 칼럼 B로 정렬된다. 

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_248.jpg)

1. 인덱스 유일 스캔

   유일 인덱스(Unique Index)를 사용하여 **단 하나의 데이터를 추출**하는 방식이다. 유일 인덱스는 중복을 허락하지 않는 인덱스이다. 유일 인덱스 구성 칼럼에 모두 '='로 값이 주어지면 결과는 최대 1건이 된다. 인덱스 유일 스캔은 유일 인덱스 구성 칼럼에 대해 모두 ‘=’로 값이 주어진 경우에만 가능한 인덱스 스캔 방식이다.

2. 인덱스 범위 스캔

   인덱스를 이용하여 **한 건 이상의 데이터를 추출**하는 방식이다. 유일 인덱스의 구성 칼럼 모두에 대해 ‘=’로 값이 주어지지 않은 경우와 비유일 인덱스(Non-Unique Index)를 이용하는 모든 액세스 방식은 인덱스 범위 스캔 방식으로 데이터를 액세스하는 것이다. [그림 Ⅱ-3-10]의 왼쪽 그림과 같은 방식으로 인덱스를 읽는다.



## NL & HASH & SORT MERGE 조인

> 조인이란 두 개 이상의 테이블을 하나의 집합으로 만드는 연산이다.  기술한 순서대로 통상 테이블을 조인하지만 테이블 또는 조인 결과를 이용하여 조인을 수행할 때 조인 단계별로 다른 조인 기법을 사용할 수 있다.



### (1) NL join

NL Join은 프로그래밍에서 사용하는 **중첩된 반복문과 유사한 방식**으로 조인을 수행한다.

- 외부테이블(Outer Table) : 반복문의 외부에 있는 테이블
- 내부테이블(Inner Table) : 반복문의 내부에 있는 테이블

#### 동작 원리

1. 선행 테이블에서 주어진 조건을 만족하는 행을 찾음
2. 선행 테이블의 조인 키 값을 가지고 후행 테이블에서 조인 수행
3. 선행 테이블의 조건을 만족하는 모든 행에 대해 1번 작업 반복 수행

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_250.jpg)

**①** 선행 테이블에서 조건을 만족하는 첫 번째 행을 찾음 → 이때 선행 테이블에 주어진 조건을 만족하지 않는 경우 해당 데이터는 필터링 됨 

**②** 선행 테이블의 조인 키를 가지고 후행 테이블에 조인 키가 존재하는지 찾으러 감 → 조인 시도 

**③** 후행 테이블의 인덱스에 선행 테이블의 조인 키가 존재하는지 확인 → 선행 테이블의 조인 값이 후행 테이블에 존재하지 않으면 선행 테이블 데이터는 필터링 됨 (더 이상 조인 작업을 진행할 필요 없음) 

**④** 인덱스에서 추출한 레코드 식별자를 이용하여 후행 테이블을 액세스 → 인덱스 스캔을 통한 테이블 액세스 후행 테이블에 주어진 조건까지 모두 만족하면 해당 행을 추출버퍼에 넣음 

**⑤ ~ ⑪** 앞의 작업을 반복 수행함



### (2) SORT MERGE join

Sort Merge Join은 조인 칼럼을 기준으로 데이터를 정렬하여 조인을 수행한다. 주로 스캔 방식으로 데이터를 읽는다. Sort Merge Join은 랜덤 액세스로 NL Join에서 부담이 되던 넓은 범위의 데이터를 처리할 때 이용되던 조인 기법이다. 하지만 성능이 떨어질 가능성이 존재한다.

 Sort Merge Join은 Hash Join과는 달리 동등 조인 뿐만 아니라 비동등 조인에 대해서도 조인 작업이 가능하다는 장점이 있다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_251.jpg)

#### 동작 원리

**①** 선행 테이블에서 주어진 조건을 만족하는 행을 찾음 

**②** 선행 테이블의 조인 키를 기준으로 정렬 작업을 수행 ① ~ ②번 작업을 선행 테이블의 조건을 만족하는 모든 행에 대해 반복 수행 

**③** 후행 테이블에서 주어진 조건을 만족하는 행을 찾음 

**④** 후행 테이블의 조인 키를 기준으로 정렬 작업을 수행 ③ ~ ④번 작업을 후행 테이블의 조건을 만족하는 모든 행에 대해 반복 수행 

**⑤** 정렬된 결과를 이용하여 조인을 수행하며 조인에 성공하면 추출버퍼에 넣음



### (3) HASH join

조인을 수행할 테이블의 조인 칼럼을 기준으로 해쉬 함수를 수행하여 서로 동일한 해쉬 값을 갖는 것들 사이에서 실제 값이 같은지를 비교하면서 조인을 수행한다.

Hash Join은 NL Join의 랜덤 액세스 문제점과 Sort Merge Join의 문제점인 정렬 작업의 부담을 해결 위한 대안으로 등장하였다.

Hash Join은 조인 칼럼의 인덱스를 사용하지 않기 때문에 조인 칼럼의 인덱스가 존재하지 않을 경우에도 사용할 수 있는 조인 기법이다.

Hash Join은 해쉬 함수를 이용하여 조인을 수행하기 때문에 '='로 수행하는 조인 즉, 동등 조인에서만 사용할 수 있다.

![sql가이드](http://www.dbguide.net/publishing/img/knowledge/SQL_252.jpg)

#### 동작 원리

**①** 선행 테이블에서 주어진 조건을 만족하는 행을 찾음 

**②** 선행 테이블의 조인 키를 기준으로 해쉬 함수를 적용하여 해쉬 테이블을 생성 → 조인 칼럼과 SELECT 절에서 필요로 하는 칼럼도 함께 저장됨 ① ~ ②번 작업을 선행 테이블의 조건을 만족하는 모든 행에 대해 반복 수행 

**③** 후행 테이블에서 주어진 조건을 만족하는 행을 찾음 

**④** 후행 테이블의 조인 키를 기준으로 해쉬 함수를 적용하여 해당 버킷을 찾음 → 조인 키를 이용해서 실제 조인될 데이터를 찾음 

**⑤** 조인에 성공하면 추출버퍼에 넣음 ③ ~ ⑤번 작업을 후행 테이블의 조건을 만족하는 모든 행에 대해서 반복 수행