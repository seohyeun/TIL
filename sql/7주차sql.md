### 1번문제

```sql
SELECT ANIMAl_TYPE, IFNULL(NAME, "No name"), SEX_UPON_INTAKE
FROM ANIMAL_INS
ORDER BY ANIMAL_ID 
```
- IFNULL(칼럼명, "Null일 경우 대체 값")


### 2번 문제
```sql
SELECT ANIMAL_TYPE, 
    CASE 
        WHEN NAME IS NULL THEN "No name" 
        ELSE NAME 
    END AS NAME, 
    SEX_UPON_INTAKE
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```
- CASE WHEN 칼럼명 IS NULL THEN ("Null일 경우 대체 값")
    ELSE 칼럼명
    END AS 칼럼명


### 3번 문제
```sql
SELECT ANIMAL_ID, NAME, 
       CASE 
           WHEN SEX_UPON_INTAKE LIKE '%Neutered%' OR SEX_UPON_INTAKE LIKE '%Spayed%' THEN 'O' 
           ELSE 'X' 
       END AS 중성화
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```
- LIKE '%S%' : 이름에 S 를 포함하는


### 4번 문제 

LIKE 조건 각각 평가해야 하므로    
'%Neutered%' OR '%Spayed%' 를   
SEX_UPON_INTAKE LIKE '%Neutered%' OR SEX_UPON_INTAKE LIKE '%Spayed%'로 바꿔야 한다?