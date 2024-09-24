# 2주차🍂

## 1번 문제

```sql
SELECT 
    seller_id,
    COUNT(DISTINCT order_id) AS orders
FROM 
    olist_order_items_dataset
GROUP BY 
    seller_id
HAVING 
    COUNT(DISTINCT order_id) >= 100
```
처음엔 DISTINCT 함수를 사용하지 않아서 239개의 결과가 나왔다.   
중복을 제거하기 위해 DISTINCT 함수를 사용해야 한다!   

## 2번 문제

```sql
SELECT
  *
FROM
  tips
WHERE
  size % 2 = 1
```   
모든 칼럼이 출력되어야 한다는 조건을 처음에 못 봤다..! 꼼꼼하게 읽자

## 3번 문제  
```sql  
SELECT day, ROUND(SUM(tip),2) AS tip_daily
FROM tips
GROUP BY day
ORDER BY tip_daily DESC
LIMIT 1;
```
LIMIT이 생각이 안나서 애먹었다