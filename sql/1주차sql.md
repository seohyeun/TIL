# 1주차🌝

## 데이터 타입과 데이터의 변환(CAST, SAFE_CAST)  

쿼리문에서는 SELECT 문에서 데이터를 변환시킬 수 있음  
WHERE 문에서 조건을 걸 수 있음   
데이터 타입에 따라 다양한 함수 존재   

__데이터 타입__   
: 숫자, 문자, 시간/날짜, 부울   

__데이터 타입이 중요한 이유__   
: 보이는 것과 저장된 것의 차이가 존재하기 때문!   
_ex. 빈 값이 ""인지, NULL인지_

__자료 타입 변경하기__   
CAST 함수 사용   
SELECT   
  CAST(1 AS STRING) # 숫자 1을 문자 1로 변경  

_문자열을 숫자로 바꾸는 건 오류 발생!_  
이런 경우 -> __SAFE_CAST__ 사용   
SAFE_가 붙은 함수는 변환 실패시 NULL 반환   
-> 더 안전함!   

__수학 함수__    
: 평균, 표준편차, 코사인 등   
X/Y 대신 SAFE_DIVIDE를 쓰는 이유   
-> X나 Y 중 하나가 0이여서 오류가 발생하는 경우 NULL로 반환해줌   


## 문자열 함수(CONCAT, SPLIT, REPLACE, TRIM, UPPER)   
__문자열(STRING) 함수__   
- CONCAT: 문자열 붙이기
- SPLIT: 문자열 분리하기
- REPLACE: 특정 단어 수정하기
- TRIM:문자열 자르기
- UPPER: 영어 대문자 변환  

__CONCAT__        
: 여러 문자열을 하나로 만들어줌     
-FROM이 없어도 CONCAT 인자로 STRING이나 숫자를 넣을 때는 데이터를 직접 넣어준 것이므로 실행 가능        
문법)     
SELECT    
    CONCAT(붙일 단어1, 붙일 단어2, 붙일 단어3) AS result;   

__SPLIT__      
: 단어를 쪼개줌     
-결과가 배열(ARRAY) 타입   
문법)     
SELECT  
    SPLIT(문자열 원본, 나눌 기준이 되는 문자) AS result    

__REPLACE__       
: 문자열 치환      
문법)       
SELECT
    REPLACE(문자열 원본, 찾을 단어, 바꿀 단어) AS result

__TRIM__     
: 문자열 자르기     
문법)    
SELECT
    TRIM(문자열 원본, 자를 단어) AS reult

__UPPER__    
:영어 소문자를 대문자로 변경     
문법)    
SELECT
    UPPER(문자열 원본) AS result


## 날짜 및 시간 데이터 이해하기   
시간 -> created at, updated at    
_유저의 행위에는 시간이 붙음!_    
BUT, 우리가 아는 시간 개념과 개발에서의 시간 개념은 다르다!   

__시간 데이터 다루기__   
- DATE: DATE만 표시하는 데이터 
- DATETIME: DATE + TIME 표시
- TIME: TIME만 표시하는 데이터

__타임존__
- GMT: Greenwich Mean Time(한국시간: GMT+9)
- UTC: Universal Time Coordinated(한국시간: UTC+9) -국제적인 표준 시간   
- TIMESTAMP: UTC로부터 경과한 시간을 나타내는 값, 타임존을 가지고 있음   

__시간 데이터 다루기__
- millisecond(ms): 천 분의 1초
- microsecond(us): ms의 천 분의 1초   

_예시_
- TIMESTAMP_MILLIS(1704176819711) AS milli_to_timestamp_value   
- TIMESTAMP_MICROS(1704176819711000) AS micro_to_timestamp_value
- DATETIME(TIMESTAMP_ICROS(1704176819711000)) AS datetime_value
- DATETIME(TIMESTAMP_MICROS(1704176819711), 'Asia/Seoul') AS datetime_value_asia;   
-> 데이트 타임 쓸 때는 타임존을 누락하지 말자!   

__TIMESTAMP와 DATETIME 비교__   
| | TIMESTAMP | DATETIME |
|---|---|---|
| 타임존 | UTC라고 나옴 | T가 나옴(TIME을 의미) |
| 시간 차이 | 한국 시간 -9시간 | 한국 Zone 사용시 한국 시간과 동일 |

__DATETIME 함수-CURRENT_DATETIME__   
CURRENT_DATETIME([time_zone): 현재 DATETIME 출력   
일하는 시간에는 시스템 시간과 실제 시간 간의 차이가 없을 수 있지만, 그 외 시간에는 9시간의 차이가 발생   
-> 서버와 사용자의 시간대 차이를 없애기 위해 time_zone 사용   

__DATETIME 함수-EXTRACT__    
DATETIME에서 특정 부분만 추출   
문법)
EXTRACT(part FROM datetime_expression)   
part에 month, year, day, hour, minute 등..   
- date: 2024-01-02
- day: 2

DAYOFWEEK: 요일을 추출    
일요일~토요일 : [1,7]

__DATEIME 함수-DATETIME_TRUNC__   
시간을 자를 때 사용   
DATETIME_TRUC(datetime_col, HOUR, "2024-01-02 14:42:13")   
-> 2024-01-02 14:00:00

- day: 2024-01-02T00:00:00
- year: 2024-01-01T00:00:00
- month: 2024-01-01T00:00:00
- hour: 2024-01-02T14:00:00   

__DATETIME 함수-PARSE_DATEIIME__   
__문자열__ 로 저장된 DATETIME을 __DATETIME__ 타입으로 바꿀 때 사용   
PARSE_DATETIME('문자열의 형태', 'DATETIME 문자열') AS datetime    
'문자열의 형태' -> '%Y-%m-%d %H:%M:%S'  
*Format Elements 문서 참고!   

__DATETIME 함수-FORMAT_DATETIME__
__DATETIME__ 타입 데이터를 __문자열__ 데이터로 바꿀 때 사용
SELECT
    FORMAT_DATETIME("%c", DATETIME "2024-01-02 11:22:13") AS formatted;

__DATETIME 함수-LAST DAY__
월(디폴트)의 마지막 값 반환
- last_day : 1/31
- last_day_month : 1/31
- last_day_week : 1/6(토)
- last_day_week_sun : 1/6(토)
- last_day_week_mon : 1/7(일)

__DATETIME 함수-DATETIME_DIFF__
두 DATETIME의 차이를 알고 싶은 경우
DATETIME_DIFF(첫 DATETIME, 두번쨰 DATETIME, 궁금한 차이)

궁금한 차이
- DAY (큰-작: 양수 / 작-큰: 음수)
- MONTH
- WEEK
