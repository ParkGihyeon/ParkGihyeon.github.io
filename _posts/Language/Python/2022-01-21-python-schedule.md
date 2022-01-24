---
title:  "[파이썬] 특정 시간마다 코드를 실행하는 스케줄 모듈 사용하기" 

categories:
  -  Python 
tags:
  - [Module, Schedule]

toc: true
toc_sticky: true

date: 2022-01-21
last_modified_at: 2022-01-21
---

🔔 특정 시간마다 코드를 실행하는 것을 스케줄 모듈을 사용하여 구현해 보겠습니다.

## 1️⃣ schedule 모듈 설치
터미널에 아래 명령어를 입력하여 스케줄 모듈을 설치해 주세요.
```python
pip install schedule
```
<br>

## 2️⃣ 소스코드

```python
import schedule
import time
import datetime

def job():
    now = datetime.datetime.now()
    print(now, "스케줄러 동작")

# n시간마다 실행
schedule.every(1).seconds.do(job) # 1초마다 실행
schedule.every(1).minutes.do(job) # 1분마다 실행
schedule.every(1).hours.do(job) # 1시간마다 실행
schedule.every(1).days.do(job)  # 1일마다 실행
schedule.every(1).weeks.do(job) # 1주마다 실행
schedule.every(1).hours.at("10:11").do(job) # 1시간 10분 11초마다 실행

# 매 시·분의 n시각에 실행
schedule.every().minute.at(":31").do(job) # 매분 31초에 실행
schedule.every().hour.at(":31").do(job) # 매시간 31분에 실행


# 매일 특정 시각에 실행
schedule.every().day.at("11:12").do(job) # 매일 11시 12분에 실행
schedule.every().day.at("11:12:40").do(job) # 매일 11시 12분 40초에 실행

# 특정 요일에 실행
schedule.every().monday.do(job) # 매주 월요일에 실행
schedule.every().wednesday.at("11:12").do(job) # 매주 수요일 11시 12분에 실행

while True:
    schedule.run_pending()
    time.sleep(1)
```

<br>

## 3️⃣ 실행 결과

![출력값](https://user-images.githubusercontent.com/45157347/150475942-7867239f-2f0d-4da4-a89d-550d365ff85b.JPG)

1초마다 스케줄러가 동작하였고 31초에는 '1초마다 실행'과 '매분 31초에 실행'이 함께 실행된 것을 확인할 수 있습니다.  

📌 주석을 확인하여 필요에 따라 사용하시면 됩니다.

<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}