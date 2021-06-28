# User 확장과 인증

---

1. 회원 서버에 회원가입 요청 -> 서버에 정보 등록
2. 회원 서버로 로그인 -> 서버는 로그인 정보를 통해 해당 회원이 존재하는지 확인
3. 서버 -> 회원에게 토큰 발급 -> 유효시 해당 회원정보에 해당하는 토큰 발급
4. 회원-> 서버로 토큰을 갖고 정보 요청
5. 서버-> 회원에게 토큰에 해당하는 정보 제공 



- ## 로그인

---

account 앱 생성 후 등록 (python manage.pt startapp account)  ->settings.py  'account'등록

```
<account.views.py>
from django.shortcuts import render,redirect
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm #추가
from django.contrib.auth import authenticate,login#추가

def login_view(request): #view라고 이름 짖는 이유는 auth의 login 메소드와 충돌을 방지하기 위해서이다.
    
    if request.method == "POST": #로그인 시(데이터 송시) data 저장
        form = AuthenticationForm(requset=request,data = request.POST)
        if form.is_valid():
            username= form.cleaned_data.get("username")
            password = form.cleaned_data.get("password")
            user = authenticate(request=request,username=username,password=password)
            if user is not None: #유저가 저장되었으면
                login(request,user) #로그인 후
         return redirect("mainPage")# 홈으로 이동
    else:
        form = AuthenticationForm() #로그인 폼 제공
        return render(request,'login.html',{'form':form})
```

```
<account_urls.py>
from django.urls import path
from .views import * 

urlpatterns = [
  path('login/',login_view,name='login')
]
<lionproject_urls.py>
path('account/',include('account.url')), 
```

```
<account_templates폴더_login.html> 생성
<login.html>

<h1>LoginPage</h1>

<form action="{%url 'login'%}" method="post"> <!--login path 순환연결-->
    {%csrf_token%}
    {{form.as_p}}
    <button type="submit">저장</button>
</form>
```



- ## 로그아웃 만들기

---



```
#In account.views.py
from django.shortcuts import render,redirect
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm
from django.contrib.auth import authenticate,login,logout#추가


def logout_view(request):
    logout(request)
    return rediret("mainPage")
   #로그아웃 함수 코드
```

```
<home.html>
{%if user.is_authenticated %} <!--인증된 유저라면-->
{{user.username}} <!--유저이름-->
{%endif%}
```



- ## 회원가입 

---



```
from django.contrib.auth.forms import UserCreationForm #django 기본제공 회원가입 form 


<acccount_views.py>
def register_view(request): 
    if request.method =="POST": 
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user= form.save()
            login(requset,user)
        return redirect("mainPage")

    else: 
        form = UserCreationForm() 
        return render(request,'signup.html',{'form':form})
```



```
<templates_signup.html> 생성
<h1>회원가입</h1>

<form action="{%url 'signup'%}" method="post"> 
    {%csrf_token%}

    {{form.as_p}}
    <button type="submit">저장</button>
</form>
```

```
<base.html>
    <nav class="navbar navbar-default">
        <div class="container-fluid">
          <div class="navbar-header">
            <a class="navbar-brand" href="{%url 'mainPage'%}"> 
              <img src="{%static 'test.PNG' %}" alt="" width="50" height="50"> 
            </a>
            {%if not user.is_authenticated %} <!--인증된 유저가 접속하지 않았다면-->
            <a class="navbar-login" href="{%url 'login'%}">로그인</a><!--로그인 노출-->
            {%endif%}
            {%if user.is_authenticated %}<!--인증된 유저가 접속했다면-->
            <a class="navbar-logout" href="{%url 'logout'%}">로그아웃</a> <!--로그아웃 노출-->
            {%endif%}
            <a class="navbar-signup" href="{%url 'signup'%}">회원가입</a>
          </div>
        </div>
      </nav>
  <!--로그인 시 로그인 url은 보이지 않고 로그아웃 버튼만 보임-->

```



- ## 유저 확장

```
<account_models.py>
from django.db import models

from django.contrib.auth.models import AbstractUser
# Create your models here.

class CustomUser(AbstractUser): #기본 칼럼을 상속
    nickname = models.CharField(max_Length=100) #이름
    university = models.CharField(max_Length=50) #대학교
    location = models.CharField(max_Length=20) #거주지 
```

```
<likelion_setting.py> 
AUTH_USER_MODEL = 'account.CustomUser' #추가
```

이후 makemigrations , migrate (터미널)



```
<account.forms.py>생성
from django.contrib.auth.forms import UserCreationForm  #상속
from .models import CustomUser

class RegisterForm(UserCreationForm): #UserCreationForm을 사용자 정의 class에 상속.
    class Meta:
        model = CustomUser #.models의 유저 class 받아옴
        fields = ['username','password1','password2','nickname','location','university'] 
```



```
<account_views.py>
def register_view(request): 
    if request.method =="POST":
        form = RegisterForm(request.POST) #UserCreationForm -> RegisterForm
        if form.is_valid():
            user= form.save()
            login(requset,user)
        return redirect("mainPage")

    else:
        form = RegisterForm() #UserCreationForm -> RegisterForm
        return render(request,'signup.html',{'form':form})
```



```
<account.admin.py>
admin.site.register(CustomUser)추가
```