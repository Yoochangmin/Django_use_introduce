# MTV패턴 :(Model, Template, View)



Teplate : 사용자가 보이는 영역 // HTML CSS 템플릿 언어

Model : DataBase

View: 사용자로부터 요청을 ㅇ받고 데이터를 처리하는 곳



## 가상환경(python 3.9.2 , Django 3.2.3)

1. 가상환경 만들기 :  python -m venv mydjango(이름)
2. 가상환경 실행    : source mydjango/script/activate
3. 프로젝트 만들기 :django-admin startproject firstproject(이름)
4. 프로젝트 실행을 위한 파일 이동 : cd firstproject
5. 서버 실행 : python manage.py runserver

# APP:

앱 실행 만들기 : python manage.py startapp firstapp

앱등록 : firstproject->settings  -> 'firstapp.apps.Firstappconfig',   //앱실행 준비(등록)

