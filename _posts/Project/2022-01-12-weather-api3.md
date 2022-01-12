---
title:  "[기상청 API: 파이썬] 한국 기상청 API 호출하기 (1)" 

categories:
  -  Project 
tags:
  - [Python, Json]

toc: true
toc_sticky: true

date: 2022-01-12
last_modified_at: 2022-01-12
---

🔔 한국 기상청 api를 호출하여 기상정보를 확인하는 과정을 알아보겠습니다.

## 1️⃣ 공공데이터포털 가입하기
<https://www.data.go.kr/index.do>

우선, 오픈 API를 사용하기 위해 위 주소에 접속하여 공공데이터포털에 가입해주세요.

<https://www.data.go.kr/data/15084084/openapi.do>

그 다음으로는 기상청 API 상세페이지로 접속하여 활용신청을 해주시면 됩니다.

![기상청](https://user-images.githubusercontent.com/45157347/149079067-479c0a04-9d7e-41da-8b61-2640c5e59880.JPG)

1. 활용 목적 입력
2. 상세기능정보 선택 (모두 선택)
3. 라이선스 표시 동의

<br>

![승인](https://user-images.githubusercontent.com/45157347/149079960-4a196130-6b90-41d5-ae5c-04fde7467dd7.JPG)
- 신청 완료후 우측 상단의 마이페이지로 들어간 뒤 방금 신청한 기상청 조회서비스 클릭

<br>
 
![인증키](https://user-images.githubusercontent.com/45157347/149080255-ca2e8107-152d-47bb-85b4-eb90e8bb3425.JPG)
- 일반 인증키(Encoding)가 API 호출에 사용할 개인 key입니다. 
- 단, 신청된 API는 1~2시간 후 호출이 가능합니다.

<br>

## 2️⃣ 기상청 API 호출 소스코드
아래는 위경도 좌표를 입력하면 기상청 격자계 좌표로 변환한 뒤 현재 시각에서 가장 가까운 예보 시간을 탐색하여 향후 날씨 예보를 출력하는 소스코드입니다.

```python
import time
import datetime
import math
import requests
import numpy as np
import pprint

# 위경도 좌표를 기상청 격자계 좌표로 변환
def grid(v1, v2):
    RE = 6371.00877 # 지구 반경(km)
    GRID = 5.0      # 격자 간격(km)
    SLAT1 = 30.0    # 투영 위도1(degree)
    SLAT2 = 60.0    # 투영 위도2(degree)
    OLON = 126.0    # 기준점 경도(degree)
    OLAT = 38.0     # 기준점 위도(degree)
    XO = 43         # 기준점 X좌표(GRID)
    YO = 136        # 기1준점 Y좌표(GRID)

    DEGRAD = math.pi / 180.0
    location = []  # 좌표를 담을 배열
    re = RE / GRID
    slat1 = SLAT1 * DEGRAD
    slat2 = SLAT2 * DEGRAD
    olon = OLON * DEGRAD
    olat = OLAT * DEGRAD

    sn = math.tan(math.pi * 0.25 + slat2 * 0.5) / math.tan(math.pi * 0.25 + slat1 * 0.5)
    sn = math.log(math.cos(slat1) / math.cos(slat2)) / math.log(sn)
    sf = math.tan(math.pi * 0.25 + slat1 * 0.5)
    sf = math.pow(sf, sn) * math.cos(slat1) / sn
    ro = math.tan(math.pi * 0.25 + olat * 0.5)
    ro = re * sf / math.pow(ro, sn)
    rs = {}

    ra = math.tan(math.pi * 0.25 + (v1) * DEGRAD * 0.5)
    ra = re * sf / math.pow(ra, sn)

    theta = v2 * DEGRAD - olon
    if theta > math.pi:
        theta -= 2.0 * math.pi
    if theta < -math.pi:
        theta += 2.0 * math.pi
    theta *= sn
    rs['x'] = math.floor(ra * math.sin(theta) + XO + 0.5)
    rs['y'] = math.floor(ro - ra * math.cos(theta) + YO + 0.5)
    location.insert(0, rs['x'])
    location.insert(1, rs['y'])

    return location

# 현재 시각에서 가장 가까운 예보 시간을 탐색
def find_nearest(now_hour):
    time_array = [2, 5, 8, 11, 14, 17, 20, 23]

    today = datetime.date.today()
    today_str = today.strftime("%Y%m%d")

    basedate = today_str

    yesterday = today - datetime.timedelta(1)
    yesterday_str = yesterday.strftime("%Y%m%d")

    now_hour = int(now_hour[:2])
    time_array = np.asarray(time_array)
    idx = (np.abs(time_array - now_hour)).argmin()

    if time_array[idx] > now_hour:
        idx = idx - 1

    if now_hour < 2:
        basedate = yesterday_str
        basetime = "2300"
    elif time_array[idx] < 10:
        basetime = "0" + str(time_array[idx]) + "00"
    else:
        basetime = str(time_array[idx]) + "00"

    return basedate, basetime



def kma_next_hourly(lat, lng):
    vilage_weather_url = "http://apis.data.go.kr/1360000/VilageFcstInfoService_2.0/getVilageFcst?"
    service_key = "일반 인증키(Encoding)"

    # 위경도 좌표를 기상청 격자계 좌표로 변환
    location_result = grid(lat, lng)

    hour_str = time.strftime('%H')
    now_hour = hour_str + "00"

    # 현재 시각에서 가장 가까운 예보 시간을 선택
    base_date, base_time = find_nearest(now_hour)

    nx = str(location_result[0])
    ny = str(location_result[1])

    # nouOfRows: 호출 개수 (많을수록 장기 예보를 호출)
    # base_date: 예보가 올라온 날짜를 지정
    # base_time: 예보가 올라온 시간을 지정
    # nx, ny: 좌표값
    payload = "serviceKey=" + service_key + "&" +\
        "&pageNo=1"+ "&" +\
        "&numOfRows=20"+ "&" +\
        "dataType=json" + "&" +\
        "base_date=" + base_date + "&" +\
        "base_time=" + base_time + "&" +\
        "nx=" + nx + "&" +\
        "ny=" + ny

    # 값 요청
    res = requests.get(vilage_weather_url + payload)
    items = res.json().get('response').get('body').get('items')

    return items


# 원하는 지역의 좌표를 입력.
# 구글 지도에서 우클릭 하여 좌표 획득
pprint.pprint(kma_next_hourly(33.4889179032603, 126.498229141199))
```

예보 호출 방법은 'kma_next_hourly(위도, 경도)' 이며 'numOfRows=정수'를 크게 입력하면 조금 더 먼 날짜의 예보도 호출이 가능합니다.  

<br>

### 호출 결과값

```json
{'item': [{'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'TMP',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '4',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'UUU',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '3.9',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'VVV',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '-3.9',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'VEC',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '315',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'WSD',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '5.5',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'SKY',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '4',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'PTY',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '0',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'POP',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '30',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'WAV',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '0.5',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'PCP',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '강수없음',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'REH',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '70',
           'nx': 52,
           'ny': 38},
          {'baseDate': '20220112',
           'baseTime': '1400',
           'category': 'SNO',
           'fcstDate': '20220112',
           'fcstTime': '1500',
           'fcstValue': '적설없음',
           'nx': 52,
           'ny': 38},

           이하 생략 ···
```
- json 출력값입니다.  

<br>

### json 데이터 분석

1. base는 예보가 올라온 날짜와 시간입니다.
2. category는 예보 항목입니다. (강수확률, 온도, 날씨 등)
3. fcst는 각 항목의 시간입니다. (향후 예보들의 시간)
4. fcstValue는 각 항목의 값입니다. (강수확률 값, 온도 값, 날씨 코드 등)

현재는 'numOfRows=20'으로 API를 호출하였지만 넉넉한 값을 잡아 필요한 날짜만큼 데이터를 호출할 수 있습니다. (값이 커지면 호출에 소요되는 시간이 길어집니다.)

<br> 

![코드값](https://user-images.githubusercontent.com/45157347/149083521-cc095f7e-9081-4960-a210-da66c604299c.JPG)
- category 항목에 대한 설명입니다.

<br>

```json
'category': 'POP 
'fcstValue': '30'
```
- POP은 강수 확률이니 30% 확률로 비가 올수도 있다는 뜻입니다.

```json
'category': 'TMP'
'fcstValue': '4'
```
- TMP는 1시간 기온이므로 4°C라는 것을 알 수 있습니다.

<br>

```json
'category': 'SKY'
'fcstValue': '1'
```
- 위처럼 SKY(하늘상태)의 경우에는 조금 다릅니다.  
fcstValue가 코드번호로 제공되며 아래 코드값에서 확인하여 사용해야 합니다.  

<br>

```json
'category': 'PTY'
'fcstValue': '0'
```
- PTY(강수형태)는 SKY(하늘상태)와 함께 짝을 이루며 본 코드가 단기 예보를 호출하고 있기 때문에 초단기 코드가 아닌 단기 코드를 확인해야 합니다.
- 📌 PTY(강수형태)의 값이 0인 경우에는 SKY(하늘상태)를 날씨로 제공하면 되고 0이 아닌 경우에는 PTY(강수형태)를 날씨로 제공하면 됩니다.

<br> 

![코드](https://user-images.githubusercontent.com/45157347/149084494-cae699be-b5f9-4e22-a501-20c979d3b654.JPG)

<br>

다음 시간에는 위경도 좌표를 주소를 통해 검색하는 과정을 알아보겠습니다!

***
<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}