---
title:  "[기상청 API: 파이썬] OpenWeatherMap 오픈웨더맵 API 호출하기 (1)" 

categories:
  -  Project 
tags:
  - [Python, Json]

toc: true
toc_sticky: true

date: 2022-01-06
last_modified_at: 2022-01-06
---

🔔 OpenWeatherMap api를 호출하여 기상정보를 확인하는 과정을 알아보겠습니다.

## 1️⃣ OpenWeatherMap 가입하기
<https://openweathermap.org/>

API를 호출하기 위해서는 개인 Key가 필요합니다.  
위 주소에 접속하여 회원가입을 먼저 해주세요.

가입 과정은 아래와 같습니다.

1. 우측 상단 Sign in 클릭
2. Create an Account 클릭
3. 필요 정보 입력, 체크박스 체크
  
![가입](https://user-images.githubusercontent.com/45157347/148322457-096d2149-0670-458d-a92d-9543063783c6.JPG)

아래 체크박스 2개는 필수적으로 체크해야 합니다.

☑ I am 16 years old and over  
☑ I agree with Privacy Policy, Terms and conditions of sale and Websites terms and conditions of use

<br>

이메일로 제공받고자 하는 항목에 체크하시면 됩니다.  
저는 API 호출 횟수를 초과하는 경우를 확인하기 위해 첫번째만 체크했습니다.

☑ System news (API usage alert, system update, temporary system shutdown, etc)  
□ Product news (change to price, new product features, etc)  
□ Corporate news (our life, the launch of a new service, etc)

<br>

## 2️⃣ API 신청하기
로그인 후 메인페이지 상단의 API 메뉴로 접근하여 원하는 API를 선택해야 합니다.  
이번 시간에는 **One Call API**를 사용해 보겠습니다.  
**API doc**를 클릭하여 호출 형식을 확인할 수 있습니다.

![api](https://user-images.githubusercontent.com/45157347/148325886-20dbc233-5584-4e84-b275-198af34e9168.JPG)

<br>

## 3️⃣ API 호출하기
사실 OpenWeatherMap의 API를 호출하는 것은 굉장히 간단합니다.  
위도, 경도, 개인 key만 입력하면 아래 예제로 날씨 데이터를 호출할 수 있습니다.

```python
import requests
import pprint # print를 보기 좋게 만드는 모듈

# 원하는 지역의 위경도값 (예제는 당산역)
lat = 35.86582227007494
lng = 126.902001

# 개인 key
api_key = '개인 key를 입력해 주세요.'

api_call = 'https://api.openweathermap.org/data/2.5/onecall?appid=' + api_key
api_call += '&lat=' + str(lat) + '&lon=' + str(lng) + '&exclude=' + 'alerts,daily,minutely' + '&lang=kr'

# json 응답 저장
w_dataset = requests.get(api_call).json()

# 날씨 json 출력
pprint.pprint(w_dataset)
```

lat : 위도 좌표  
lng : 경도 좌표  
api_key : OpenWeatherMap 개인 key  
1. OpenWeatherMap 상단 메뉴에서 자신의 아이디 클릭
2. My API Keys 클릭
3. Key값 복사  

exclude : API 호출시 필요하지 않은 데이터를 선택할 수 있습니다. (지금은 신경쓰지 않아도 됩니다.)

<br>

### 호출 결과값
pprint.pprint(w_dataset) 즉, 호출된 json은 다음과 같습니다.

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

다음 시간에는 호출된 json 데이터를 입맛대로 사용하는 방법을 알아보도록 하겠습니다.






***
<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}