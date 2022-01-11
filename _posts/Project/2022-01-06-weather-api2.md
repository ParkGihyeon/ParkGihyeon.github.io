---
title:  "[기상청 API] OpenWeatherMap 오픈웨더맵 API 호출하기 (2)" 

categories:
  -  Project 
tags:
  - [Python, Json]

toc: true
toc_sticky: true

date: 2022-01-06
last_modified_at: 2022-01-06
---

🔔 호출한 OpenWeatherMap api를 처리하는 방법을 알아보겠습니다.  
📌 OpenWeatherMap 가입 및 API 호출 과정은 이전 게시글을 참고해주세요.

## 1️⃣ 호출 결과값 JSON
pprin t.pprint(w_dataset) 즉, 호출된 json은 다음과 같습니다.

```json
{'current': {'clouds': 0,        
             'dew_point': 265.84,
             'dt': 1641442597,   
             'feels_like': 274.68,
             'humidity': 38,
             'pressure': 1028,
             'sunrise': 1641422582,
             'sunset': 1641457956,
             'temp': 278.05,
             'uvi': 1.98,
             'visibility': 10000,
             'weather': [{'description': '맑음',
                          'icon': '01d',
                          'id': 800,
                          'main': 'Clear'}],
             'wind_deg': 344,
             'wind_gust': 6.12,
             'wind_speed': 4.37},
 'hourly': [{'clouds': 0,
             'dew_point': 265.84,
             'dt': 1641441600,
             'feels_like': 274.68,
             'humidity': 38,
             'pop': 0,
             'pressure': 1028,
             'temp': 278.05,
             'uvi': 1.98,
             'visibility': 10000,
             'weather': [{'description': '맑음',
                          'icon': '01d',
                          'id': 800,
                          'main': 'Clear'}],
             'wind_deg': 344,
             'wind_gust': 6.12,
             'wind_speed': 4.37},
            {'clouds': 0,
             'dew_point': 265.79,
             'dt': 1641445200,
             'feels_like': 274.38,
             'humidity': 38,
             'pop': 0,
             'pressure': 1028,
             'temp': 277.98,
             'uvi': 1.59,
             'visibility': 10000,
             'weather': [{'description': '맑음',
                          'icon': '01d',
                          'id': 800,
                          'main': 'Clear'}],
             'wind_deg': 340,
             'wind_gust': 6.46,
             'wind_speed': 4.78}, 
             
             이하 생략 ···
```

현재 날씨는 current를, 1시간 간격의 예보는 hourly를 사용하시면 됩니다.  

pop : 강수확률  
temp : 기온  
weather-icon : 날씨 코드(날씨 아이콘 이미지 호출에 사용됨)   
weather-main : 날씨  

<br>

## 2️⃣ JSON 데이터 다루기

호출된 JSON 데이터를 다루는 방법 또한 호출만큼 간단합니다.

우선, 크게 current 데이터(현재 날씨)와 hourly 데이터(시간별 예보)로 나뉩니다.  

### 1. 현재 날씨 데이터
**현재 날씨 모두를 가져오고 싶다면** 다음과 같이 JSON 데이터 w_dataset에서 current 데이터만 골라내면 됩니다.

```python
w_dataset['current']
```


**현재 날씨에서 기온을 가져오고 싶다면**

```python
w_dataset['current']['temp']
```


**현재 날씨(기상)**도 마찬가지로 순서대로 내려가면 됩니다.  
단, weather의 값은 배열(0번 배열만 존재)로 되어있어서 [0]['main']으로 지정해야 합니다.

```python
w_dataset['current']['weather'][0]['main']
```

<br>

### 2. 시간별 예보 데이터
시간별 예보 hourly도 비슷합니다.  
우선 hourly를 선택한 다음 어느 시간의 예보를 선택할지 정해야 합니다.  
**시간별 예보**는 배열의 각 인덱스에 있기 때문에 다음 순서로 선택합니다.  
[예보]->[시간 인덱스 번호]->[weather데이터][0]->[main데이터]

```python
w_dataset['hourly'][11]
```

⭐ current 데이터가 현재 시각이기 때문에 horuly의 0번 인덱스가 1시간 뒤의 예보입니다.    
따라서 **w_dataset['hourly'][11]**는 12시간 뒤의 예보 데이터입니다. ⭐

**날씨(기상)** 데이터에 접근하는 것도 마찬가지로 아래처럼 하시면 됩니다.
```python
w_dataset['hourly'][11]['weather'][0]['main']
```

<br>

### 3. 날짜 형식 변환
JSON 데이터를 유심히 보신 분들은 눈치채셨겠지만 날짜의 형식이 평소 사용하던 것과는 달라 알아볼 수가 없습니다.
인덱스 번호로 시간을 유추하는 방법도 있겠지만 저는 이것을 **'2022-01-06 12:00'**처럼 추후에 사용하기 쉽도록 형식을 변경하였습니다.

반복문을 돌리면서 아래 함수를 사용하여 형변환을 해주시면 됩니다.
형식을 
```python
import time

def epochtime_to_datetime(epoch_time):
    time_formatted = time.strftime('%Y-%m-%d %H:%M', time.localtime(epoch_time))
    return time_formatted
```




***
<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}