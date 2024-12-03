### PIVOT절과 UNPIVOT절

- PIVOT과 UNPIVOT이 필요한 이유: 가시성이 확보된 데이터 형태와 집계함수를 사용할 수 있는 데이터 형태가 다르기 때문

가시성 확보된 데이터 형태 -> 집계함수 사용 불가
so, 집계함수를 쓸 수 있게 변경 후 처리

반대로 집계함수 기능 사용 가능한 데이터 형태 -> 가시성 있게 표현 불가 
so, 형태 변경 필요

__PIVOT__   
: 행 -> 열

PIVOT절 예시
```sql
PIVOT (COUNT(*) FOR DNAME IN 
('ACCOUNTING' AS ACCOUNTING,
'RESEARCH' AS RESEARCH,
 'SALES' AS SALES)):
```
- SELECT, FROM절 다음에 붙음

__UNPIVOT__    
: 열 -> 행    
PIVOT의 반대

UNPIVOT절 예시

```sql
UNPIVOT (기온 FOR 연도 IN(
    Y2018 AS '2018년', 
    Y2019 AS '2019년', 
    Y2020 AS '2020년', 
    Y2021 AS '2021년', 
    Y2022 AS '2022년' ) )
```


### SQL 쿼리 성능 최적화 팁

_Before_
인덱싱: 데이터베이스에서 원하는 정보를 찾기 위한 '지름길'을 만드는 것

1. 좌변을 연산하지 않을 것
데이터의 원본을 변형하여 서치하는 연산 -> 한 줄 한 줄 일일이 연산 수행 후 찾아야 하기 때문에 쿼리 성능 저하   
So, __우변에서의 데이터 필터링__ 추천!(데이터 변형 x)   

ex)
```sql
SELECT * FROM sales WHERE YEAR(date) = 2021;
```
위의 쿼리 대신 아래의 쿼리 사용
```sql
SELECT * FROM sales 
WHERE date >= '2021-01-01' AND date <= '2021-12-31';
```

2. OR 대신 UNION 사용
OR 연산자: 한 번의 스캔으로 모든 조건 확인해야 함
단일 값에 대한 빠른 검색을 위해 최적화된 인덱스의 장점을 살리지 못함
So, __UNION 사용__ 추천!

ex)
```sql
SELECT * FROM employees 
WHERE department = 'Marketing' OR department = 'IT';
```

```sql
SELECT * FROM employees WHERE department = 'Marketing'
UNION
SELECT * FROM employees WHERE department = 'IT';
```
각각 인덱스를 통한 빠르게 처리 후, UNION을 통해 결과 합침
중복이 없다면 UNION ALL 사용 (UNION과 달리 중복 제거 단계 생략)

3. 필요한 Row와 Column만 선택하여 성능 최적화
특정 조건을 만족하는 Row만 선택함으로써 데이터베이스가 처리해야 할 데이터 양 최소화

ex)
```sql
SELECT name, email
FROM employees
WHERE department = 'Marketing' AND sales >= 100000;
```
-> 마케팅 부서 + 매출액 100,000 이상만 선택

```sql
SELECT e.name, e.department, e.sales
FROM employees e
JOIN (
  SELECT department, MAX(sales) AS max_sales
  FROM employees
  GROUP BY department
) d ON e.department = d.department AND e.sales = d.max_sales;
```
-> 서브쿼리 활용하여 부서별 최대 매출액 계산, department와 max_sales만 선택

4. 분석 함수를 활용하여 쿼리 성능 향상
분석 함수: ROW_NUMBER(), RANK(), DENSE_RANK(), LEAD(), LAG() 등
1) 전통적 집계 함수와 달리 사전에 데이터 그룹화 필요 x
2) 중간 결과물의 저장, 재처리 최소화

- 순위함수: ROW_NUMBER(), RANK(), DENSE_RANK()
- 데이터 변화 추적: LEAD(), LAG()
- 데이터 필터링 함수: ROW_NUMBER() (TOP n개 추출 가능)

5. 와일드카드(%) 끝에 작성
ex) '%John' -> 'John%'
-> 데이터베이스가 인덱스를 활용해서 검색 범위를 효과적으로 좁힘

6. 계산값을 미리 저장해두었다가, 나중에 조회
실시간 조회 -> 쿼리 성능 저하

ex)
```sql
SELECT 
    p.product_id,
    AVG(od.quantity * od.unit_price) AS avg_order_amount,
    SUM(od.quantity * od.unit_price) AS total_sales,
    COUNT(DISTINCT o.customer_id) AS num_purchasers,
    COUNT(DISTINCT CASE WHEN o.customer_id IN (
        SELECT customer_id 
        FROM orders
        WHERE product_id = p.product_id
        GROUP BY customer_id
        HAVING COUNT(*) > 1
    ) THEN o.customer_id END) * 1.0 / COUNT(DISTINCT o.customer_id) AS repurchase_rate
FROM 
    products p
    JOIN order_details od ON p.product_id = od.product_id
    JOIN orders o ON od.order_id = o.order_id
GROUP BY 
    p.product_id;
```
-> 쿼리가 실행될때마다 데이터를 모두 읽고 계산 수행

```sql
CREATE TABLE product_stats AS
SELECT
    p.product_id,
    AVG(od.quantity * od.unit_price) AS avg_order_amount,
    SUM(od.quantity * od.unit_price) AS total_sales,
    COUNT(DISTINCT o.customer_id) AS num_purchasers,
    COUNT(DISTINCT CASE WHEN o.customer_id IN (
        SELECT customer_id
        FROM orders
        WHERE product_id = p.product_id
        GROUP BY customer_id
        HAVING COUNT(*) > 1
    ) THEN o.customer_id END) * 1.0 / COUNT(DISTINCT o.customer_id) AS repurchase_rate
FROM
    products p
    JOIN order_details od ON p.product_id = od.product_id
    JOIN orders o ON od.order_id = o.order_id
GROUP BY
    p.product_id;
```
-> product_stats 라는 새 테이블 생성 후 통계치 미리 저장

+ 계산 결과를 저장하고 주기적으로 업데이트 -> 복잡한 실시간 쿼리의 부담을 크게 줄임