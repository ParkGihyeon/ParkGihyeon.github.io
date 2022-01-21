---
title:  "[OpenGL: C++] OpenGL을 사용하여 간단하게 자동차 그리기" 

categories:
  -  Project 
tags:
  - [OpenGL, C++]

toc: true
toc_sticky: true

date: 2022-01-21
last_modified_at: 2022-01-21
---

🔔 glut.h 다운로드 및 환경 구성은 이전 게시글에 설명되어 있습니다.

## 1️⃣ 소스코드

```cpp
#include <vector>
#include <GL/glut.h>
#include <GL/gl.h>
#include <GL/GLU.H>

void InitLight(); //광원 & 재질 설정 함수
void reshape(int w, int h); //reshape콜백 함수
void display(); //자동차 & XYZ축 출력 함수
void special(int key, int x, int y); //특수키 입력 함수

int win_width = 600; //창 좌우 길이
int win_height = 600; //창 위아래 길이
double win_aspect_ratio = ((double)win_width / (double)win_height); //시야 설정에 사용
const double PI = 3.141593; //파이값 정의
double cam_dist = 5.0;
double cam_theta = 0.0; //z축과 이루는 각도 0도로 초기화
double cam_delta = 90 * PI / 180; //y축과 이루는 각도 90도로 초기화 *PI/180 필수


void main(int argc, char** argv)
{
	glutInit(&argc, argv);
	glutInitWindowSize(win_width, win_height);
	glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH);
	glutCreateWindow("Computer Graphics");

	InitLight(); //광원 & 재질 관련

	glutReshapeFunc(reshape); //reshape콜백 함수
	glutDisplayFunc(display); //자동차 & XYZ축 출력
	glutSpecialFunc(special); //특수키 입력

	glutMainLoop();
}


void InitLight() { //광원 & 재질 설정
	GLfloat mat_diffuse[] = { 0.5, 0.4, 0.3, 0.1 }; //재질 관련
	GLfloat mat_specular[] = { 1.0, 1.0, 1.0, 1.0 };
	GLfloat mat_ambient[] = { 0.5, 0.4, 0.3, 0.1 };
	GLfloat mat_shininess[] = { 15.0 };

	GLfloat light_specular[] = { 1.0, 1.0, 1.0, 1.0 }; //광원 관련
	GLfloat light_diffuse[] = { 0.8, 0.8, 0.8, 1.0 };
	GLfloat light_ambient[] = { 0.3, 0.3, 0.3, 1.0 };
	GLfloat light_position[] = { 0.0, 0.0, 1.0, 0.0 };
	glShadeModel(GL_SMOOTH);

	glEnable(GL_COLOR_MATERIAL); //조명 활성화 뒤 물체의 색상이 나오도록
	glEnable(GL_DEPTH_TEST); //깊이버퍼 활성화
	glEnable(GL_LIGHTING); //조명 활성화
	glEnable(GL_LIGHT0); //0번 조명 활성화

	glLightfv(GL_LIGHT0, GL_POSITION, light_position); //0번 조명 설정
	glLightfv(GL_LIGHT0, GL_DIFFUSE, light_diffuse);
	glLightfv(GL_LIGHT0, GL_SPECULAR, light_specular);
	glLightfv(GL_LIGHT0, GL_AMBIENT, light_ambient);

	glMaterialfv(GL_FRONT, GL_DIFFUSE, mat_diffuse); //앞면 질감 설정
	glMaterialfv(GL_FRONT, GL_SPECULAR, mat_specular);
	glMaterialfv(GL_FRONT, GL_AMBIENT, mat_ambient);
	glMaterialfv(GL_FRONT, GL_SHININESS, mat_shininess);
}


void setupProjection() //투사(시야) 설정
{
	glMatrixMode(GL_PROJECTION); //투상좌표계의 공간을 계산하기 위해
	glLoadIdentity(); //좌표계 초기화

	gluPerspective(45.0, win_aspect_ratio, 0.1, 100.0);	//90도(각도) 만큼 관찰 가능
}


void setupViewing() //카메라(시점) 설정
{
	glMatrixMode(GL_MODELVIEW); //모델,시점좌표계의 공간을 계산하기 위해
	glLoadIdentity(); //좌표계 초기화

	double cam_x = cam_dist * sin(cam_theta) * sin(cam_delta); //x축 변화에 대한 공식
	double cam_y = cam_dist * cos(cam_delta); //y축 변화에 대한 공식
	double cam_z = cam_dist * cos(cam_theta) * sin(cam_delta); //z축 변화에 대한 공식

	gluLookAt(cam_x, cam_y, cam_z, 0, 0, 0, 0, 1, 0); //카메라의 위치를 설정
}


void drawAxes() //X,Y,Z축을 그리는 함수, 카메라가 회전함을 보이기 위해
{
	glBegin(GL_LINES);

	glColor3f(1, 0, 0); //빨강 x축
	glVertex3d(0, 0, 0);
	glVertex3d(10, 0, 0);

	glColor3f(0, 1, 0); //초록 y축
	glVertex3d(0, 0, 0);
	glVertex3d(0, 10, 0);

	glColor3f(0, 0, 1); //파랑 z축
	glVertex3d(0, 0, 0);
	glVertex3d(0, 0, 10);

	glEnd();
}


void display()
{
	glClearColor(1, 1, 1, 1); //배경색 지정
	glClearDepth(1); //깊이 버퍼를 삭제
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); //물체들을 지움

	setupProjection(); //투사(시야) 설정
	setupViewing(); //카메라(시점) 설정


	glMatrixMode(GL_MODELVIEW); //모델,시점좌표계의 공간을 계산하기 위해


	drawAxes();	//X,Y,Z축을 그림	

	glPushMatrix();
	glScalef(4, 1, 2);
	glColor3f(0.5, 0.5, 0.5);
	glutSolidCube(0.5); //몸체

	glTranslatef(-0.05, 0.3, 0);
	glScalef(0.6, 3, 2);
	glColor3f(0.1, 0.5, 0.9);
	glutSolidCube(0.24); //옆창문

	glTranslatef(-0.12, 0.001, -0.001);
	glScalef(1, 1.8, 2.48);
	glRotatef(230, 0, 0, 250);
	glColor3f(0.1, 0.5, 0.9);
	glutSolidCube(0.096); //앞창문
	glPopMatrix();

	glTranslatef(0, 0, 0.5);
	glPushMatrix();
	glTranslatef(-0.4, -0.2, 0);
	glColor3f(0, 0, 0);
	glutSolidTorus(0.1, 0.2, 8, 8); //바퀴
	glTranslatef(1, 0, 0);
	glutSolidTorus(0.1, 0.2, 8, 8); //바퀴
	glPopMatrix();
	glTranslatef(0, 0, -1);
	glPushMatrix();
	glTranslatef(-0.4, -0.2, 0);
	glutSolidTorus(0.1, 0.2, 8, 8); //바퀴
	glTranslatef(1, 0, 0);
	glutSolidTorus(0.1, 0.2, 8, 8); //바퀴
	glPopMatrix();

	glutSwapBuffers();
}


void reshape(int w, int h) //reshape콜백 함수
{
	win_width = w;
	win_height = h;
	win_aspect_ratio = (double)h / (double)w;

	glViewport(0, 0, win_width, win_height); //뷰포트 설정
}


void special(int key, int x, int y) //특수키 입력에/
{
	switch (key) {
	case GLUT_KEY_RIGHT://방향키 오른쪽
		cam_theta += PI / 180.0;
		break;

	case GLUT_KEY_LEFT://방향키 왼쪽
		cam_theta -= PI / 180.0;
		break;

	case GLUT_KEY_DOWN://방향키 아래쪽
		if (cam_delta > 150 * PI / 180)	//각도가 30도~150 도이므르 150도가 
			cam_delta = 150 * PI / 180; //150도가 넘으면 150도로 초기화 시킵니다
		cam_delta += PI / 180.0;
		break;

	case GLUT_KEY_UP://방향키 위쪽
		if (cam_delta < 30 * PI / 180) //각도가 30도~150 도이므르 150도가 
			cam_delta = 30 * PI / 180; //30도보다 작아지면 30도로 초기화 시킵니다
		cam_delta -= PI / 180.0;
		break;

	case GLUT_KEY_PAGE_UP://페이지업
		cam_dist -= 0.1;
		if (cam_dist > 10)   //cam_dist는 0~10까지로 한정해 줍니다
			cam_dist = 10;		//0보다 커지면 10으로 초기화 시킵니다
		break;

	case GLUT_KEY_PAGE_DOWN://페이지다운
		cam_dist += 0.1;
		if (cam_dist > 10)   //cam_dist는 0~10까지로 한정해 줍니다
			cam_dist = 10;		//0보다 커지면 10으로 초기화 시킵니다
		break;

	}

	glutPostRedisplay();
}
```

<br>

## 2️⃣ 실행화면

![자동차1](https://user-images.githubusercontent.com/45157347/150439539-6bc7aa86-6acd-49d9-aed3-5b99b803bc4d.JPG)


![자동차2](https://user-images.githubusercontent.com/45157347/150439530-966e700c-43d5-4c45-b37f-ef4dbf603527.JPG)

xyz축을 나타내는 3개의 선은 물체가 회전하는 것이 아닌 카메라 시점이 회전하는 것임을 보이기 위해 출력하였습니다.

***
<br>

    💾 공부한 내용을 기록하기 위한 블로그입니다.
    📄 오류와 질문은 댓글로 남겨주시면 감사하겠습니다!

[맨 위로 이동하기](#){: .btn .btn--primary }{: .align-right}