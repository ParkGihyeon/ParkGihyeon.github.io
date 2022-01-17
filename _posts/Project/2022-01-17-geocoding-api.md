---
title:  "[지오코딩 API: 파이썬] 주소로 위도 경도 찾기, 좌표로 주소 얻기" 

categories:
  -  Project 
tags:
  - [Python, Json]

toc: true
toc_sticky: true

date: 2022-01-17
last_modified_at: 2022-01-17
---

🔔 지오코딩과 역지오코딩을 간단하게 알아보겠습니다.

## 1️⃣ 별도의 가입이 필요 없는 방법

### 1. 지오코딩 (주소를 통해 위도, 경도 좌표 얻기)

```python
# 가입 없이 주소->좌표 변환
from geopy.geocoders import Nominatim

def geocoding(address):
    geolocoder = Nominatim(user_agent = 'South Korea', timeout=None)
    geo = geolocoder.geocode(address)
    crd = {"lat": str(geo.latitude), "lng": str(geo.longitude)}

    return crd

crd = geocoding("대구 태전동")
print(crd['lat'])
print(crd['lng'])
```

<br>

### 2. 역지오코딩 (위도, 경도 좌표를 통해 주소 얻기)

```python
# 가입 없이 좌표->주소 변환
from geopy.geocoders import Nominatim

def geocoding_reverse(lat_lng_str): 
    geolocoder = Nominatim(user_agent = 'South Korea', timeout=None)
    address = geolocoder.reverse(lat_lng_str)

    return address

address = geocoding_reverse('36.5760732781656, 128.15935928504484')
print(address)
```

<br>

### 3. 현재위치 좌표 얻기

```python
# 현재위치 좌표 얻기
import requests, json

def current_location():
    here_req = requests.get("http://www.geoplugin.net/json.gp")

    if (here_req.status_code != 200):
        print("현재좌표를 불러올 수 없음")
    else:
        location = json.loads(here_req.text)
        crd = {"lat": str(location["geoplugin_latitude"]), "lng": str(location["geoplugin_longitude"])}

    return crd

crd = current_location()
print(crd)
```

<br>

## 2️⃣ 카카오 API를 사용하는 방법

### 개인 Key 발급받기
1. <https://developers.kakao.com> 접속
2. 우측 상단 '내 애플리케이션' 클릭
3. 내 애플리케이션 추가하기

![앱 추가](https://user-images.githubusercontent.com/45157347/149715886-3be6c7ff-2a2b-4088-bd12-e6397189d0f5.JPG)

4. 앱 이름, 사업자명 입력 후 저장 (앱 아이콘은 필요한 경우에 업로드)

![키](https://user-images.githubusercontent.com/45157347/149716034-aad4df42-7660-428b-837d-60ce0c176b8c.JPG)

5. 생성된 '앱 이름' 클릭 (📌 'REST API 키' 복사해두기)
6. 'Web 플랫폼 등록' 클릭
7. http<temp>s://localhost:3000 입력 후 저장


<br>

### 1. 지오코딩 (주소를 통해 위도, 경도 좌표 얻기)

```python
# 카카오API를 사용하여 주소->좌표 변환
import requests, json

def get_location(address):
  url = 'https://dapi.kakao.com/v2/local/search/address.json?query=' + address
  # 'KaKaoAK '는 그대로 두시고 개인키만 지우고 입력해 주세요.
  # ex) KakaoAK 6af8d4826f0e56c54bc794fa8a294
  headers = {"Authorization": "KakaoAK 개인키"}
  api_json = json.loads(str(requests.get(url,headers=headers).text))
  address = api_json['documents'][0]['address']
  crd = {"lat": str(address['y']), "lng": str(address['x'])}
  address_name = address['address_name']

  return crd

crd = get_location("제주 애월읍")
print(crd)
```

<br>

### 2. 역지오코딩 (위도, 경도 좌표를 통해 주소 얻기)

```python
# 카카오API를 사용하여 좌표->주소 변환
import requests, json, pprint

def get_address(lat, lng):
    url = "https://dapi.kakao.com/v2/local/geo/coord2regioncode.json?x="+lng+"&y="+lat
    # 'KaKaoAK '는 그대로 두시고 개인키만 지우고 입력해 주세요.
    # ex) KakaoAK 6af8d4826f0e56c54bc794fa8a294
    headers = {"Authorization": "KakaoAK 개인키"}
    api_json = requests.get(url, headers=headers)
    full_address = json.loads(api_json.text)

    return full_address

full_address = get_address('36.5760732781656', '128.15935928504484')
pprint.pprint(full_address)
```

<br>

좌표나 주소만 입력하여 호출하면 되는 간단한 코드라서 그대로 복사하여 사용하시면 됩니다.



***
<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}