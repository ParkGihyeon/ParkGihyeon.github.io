---
title:  "[OpenGL: C++] OpenGL 사용 환경 구성하기 glut.h 다운로드" 

categories:
  -  Project 
tags:
  - [OpenGL, C++]

toc: true
toc_sticky: true

date: 2022-01-19
last_modified_at: 2022-01-19
---

🔔 비주얼스튜디오에서 OpenGL을 사용할 수 있도록 환경 구성을 해보겠습니다.

## 1️⃣ OpenGL 환경 구성하기 

티스토리 'A Cel'님의 [포스트]를 참고하였습니다.  

[포스트]: https://lynmp.com

<br>

### glut 다운로드  
<https://www.transmissionzero.co.uk/software/freeglut-devel>


![웹사이트](https://user-images.githubusercontent.com/45157347/150063775-1d659567-5c74-4cfe-8461-2d08c55f4aa0.JPG)

* 우선, 상기 웹페이지에 접속한 뒤 'Download freeglut 3.0.0 for MSVC'를 클릭하여 압축된 freeglut를 다운받아 주세요.

<br>

![폴더](https://user-images.githubusercontent.com/45157347/150064203-a338b25f-8708-44ec-9d60-05dbad23115a.JPG)

* 압축 해제한 freeglut 폴더는 위와 같은데 모두 필요하지는 않고 'include 폴더', 'lib 폴더', bin 폴더 속의 'freeglut.dll 파일'만 비주얼스튜디오 프로젝트 폴더로 옮기시면 됩니다.  
(미리 복사해두세요.)

<br>

### glut 프로젝트에 적용하기

![프로젝트생성](https://user-images.githubusercontent.com/45157347/150065466-9f8ecefa-a8bd-4237-9a01-73a641e6d7f8.JPG)

* 비주얼스튜디오 '새 프로젝트 만들기'로 들어가서 '콘솔 앱'으로 프로젝트를 생성해 주세요.

<br>

![폴더열기](https://user-images.githubusercontent.com/45157347/150065832-0cef96a6-5d69-48f2-85a6-23c18c41b4fa.JPG)

* 프로젝트명을 우클릭한 뒤 '파일 탐색기에서 폴더 열기'를 클릭하여 프로젝트 폴더를 열어주세요.

<br>

![폴더복사](https://user-images.githubusercontent.com/45157347/150066044-c01eab59-1e83-4eb1-8640-b4a9ec40889f.JPG)
 
* 아까 복사해둔 폴더와 파일을 붙여넣기 하면 파일 이동은 끝입니다.

<br>

![추가디렉터리](https://user-images.githubusercontent.com/45157347/150066315-f86194d3-7798-4482-bee5-7b6ed40ff6d9.JPG)

* 다시 프로젝트명을 우클릭한 뒤 속성에 들어갑니다.  
'구성 속성 -> C/C++ -> 일반'에서 추가 포함 디렉터리에 ./include를 입력하시면 됩니다.

<br>

![추가라이브러리](https://user-images.githubusercontent.com/45157347/150066721-eff9608e-aa3f-4825-b36a-292e5c68ee23.JPG)

* '구성 속성 -> 링커 -> 일반'에서 추가 라이브러리 디렉터리에 ./lib를 입력하면 설정은 모두 끝입니다.

<br>

## 2️⃣ 테스트

```cpp
#include "gl/glut.h"

void display() {
    glBegin(GL_POLYGON);
    glVertex2f(-0.2f, -0.2f);
    glVertex2f(0.2f, -0.2f);
    glVertex2f(0.2f, 0.2f);
    glVertex2f(-0.2f, 0.2f);
    glEnd();
    glFinish();
}

int main(int argc, char** argv)
{
    glutInit(&argc, argv);
    glutCreateWindow("glut 테스트");
    glutDisplayFunc(display);
    glutMainLoop();
    return 0;
}
```

![테스트](https://user-images.githubusercontent.com/45157347/150067481-275d1439-6488-4175-bb85-3f8cba98643f.JPG)

* 오류가 뜨지 않고 정사각형이 출력되면 성공입니다.


***
<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}