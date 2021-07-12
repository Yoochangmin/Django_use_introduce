# 웹사이트 구동 순서

------

1. ### 사용자가 서버에 요청

2. ### 서버의 view는 model에게 요청에 필요한 데이터를 받음

3. ### view는 받은 데이터를 적절하게 처리해서  template으로 넘김  

4. ### template은 받은 정보를 사용자에게 보여줌



## 가상환경(python 3.9.2 , Django 3.2.3)

1. 가상환경 만들기 :  python -m venv mydjango(이름)
2. 가상환경 실행    : source mydjango/script/activate
3. 프로젝트 만들기 :django-admin startproject firstproject(이름)
4. 프로젝트 실행을 위한 파일 이동 : cd firstproject
5. 서버 실행 : python manage.py runserver

# APP: Django프로젝트를 이루는 작은 단위 //네이버를 프로젝트라 할때 검색 메일 쇼핑등의 서비스의  app이라 생각하면  댐 //각서비스를 분류해놓은것

앱 실행 만들기 : python manage.py startapp firstapp

앱등록 : firstproject->settings  -> 'firstapp.apps.Firstappconfig',   //앱실행 준비(등록)



![실습1](C:\Users\cdals\Desktop\실습1.PNG)

![url](C:\Users\cdals\Desktop\url.PNG)

![views](C:\Users\cdals\Desktop\views.PNG)

1. ## App을 생성

2. ## Template 제작

3. ## view 제작

4. ## URL 연결







- # 템플릿 언어

  {%for word in wordDict %}

  --

  {%endfor%}

  

  

  

  ----

  {{변수명}}

