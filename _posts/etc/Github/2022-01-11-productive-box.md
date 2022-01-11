---
title:  "[깃허브] productive-box 적용하기" 

categories:
  -  Github 
tags:
  - [Git]

toc: false
toc_sticky: false

date: 2022-01-11
last_modified_at: 2022-01-11
---

🔔 깃허브에 I'm an early 🐤 혹은 night 🦉를 띄우는 방법을 알아보겠습니다.

## 1️⃣ productive-box란 무엇인가?
gist를 통해 아침형 개발자인지 저녁형 개발자인지 알아볼 수 있는 라이브러리로써  
하루를 4개의 범위로 나누어 commit을 언제 가장 많이 했는지 퍼센트로 출력한다.

![early](https://user-images.githubusercontent.com/45157347/148918595-81a76a09-6210-4322-8a43-37c1e74b77bf.JPG)

<br>
## 2️⃣ 적용을 위한 사전 작업  

1. Gist(GIST_ID) 생성
2. token(GH_TOKEN) 생성
3. 'productive-box' fork
4. 'productive-box' repository에 GIST_ID와 GH_TOKEN 입력
5. pinned에 Gist 추가

<br>
(1) 주소로 이동하여 public gist를 생성합니다.

- <https://gist.github.com/>
- Filename including extension...에 아무 이름이나 입력 ex)productivebox  
- 내용이 공백이 아니도록 아무 내용이나 입력  
- 초록박스의 화살표를 눌러 Create public gist로 변경후 초록박스 클릭  

![public_gist](https://user-images.githubusercontent.com/45157347/148921530-3bb5d52d-a3ba-4965-8ca8-3b81a187684f.JPG)

- 주소창을 보시면 'gist주소/사용자명/코드'로 이루어져 있는데 코드가 'GIST_ID'입니다.

<br>
(2) 주소로 이동하여 token을 생성합니다.

- <https://github.com/settings/tokens/new>
- Note에 아무 이름이나 입력
- Expiration 설정 (저는 만료 없음으로 설정했습니다.)
- Select scopes 항목에서 repo와 gist만 전체 체크하고 Generate token 클릭
- token값(GH_TOKEN)이 생성되는데 이 창에서 벗어나면 다시 확인할 수 없으니 주의


<br>
(3) 주소로 이동하여 'productive-box'를 fork합니다.

- <https://github.com/maxam2017/productive-box>
- 우측 상단의 'fork' 클릭하면 자동으로 페이지가 전환됩니다.
- 상단의 Action메뉴 클릭
- 가로로 기다란 ~enable 버튼 클릭
- 상단의 Code 메뉴에서 .github/workflows/schedule.yml 경로로 진입
- 우측의 연필모양 Edit this file을 클릭
- 'TIMEZONE: Asia/Seoul' 으로 변경하고 Start commit - Commit changes클릭 

<br>

- 상단 Setting 메뉴를 클릭한 뒤 Secrets 항목으로 이동
- New repository secret 버튼 클릭
- Name에 GIST_ID 입력 Value에 아까 확인한 주소창 뒷부분 코드 입력
- Name에 GH_TOKEN 입력 Value에 token 값 입력

<br>
(적용) 자신의 깃허브 프로필 메인으로 이동하여 Customize your pins를 클릭한다.  

productivebox를 체크하여 적용하면 완료! (갱신은 주기적으로 이루어짐)

📌바로 적용하고 싶다면 fork했던 repository에서 README파일에 아무거나 추가로 입력하거나 삭제하고 저장하면 갱신이 바로 이루어진다.

바로 적용되지 않는다면 조금만 기다려 보시길...




***
<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!


[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}