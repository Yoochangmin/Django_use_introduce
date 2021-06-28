# Template 상속

:html에서 중복되는 코드를 base.html을 만들어 상속시켜 만드는것

프로젝트파일에 templats폴더를 만든 뒤 base.html을 만듬



- ## 상속

---

부트스트랩을 이용하여  네비게이션바 코드 가져옴 

```
lionproject_templates_base.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BLOG</title>
    
    <!--부트스트랩 CDN-->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap-theme.min.css">
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.2/js/bootstrap.min.js"></script>
   
    <style>
        body{text-align:center;}
    </style>
</head>
   
<body>
    <h1>메인 페이지</h1>
    <!-- nav bar-->
    <nav class="navbar navbar-default">
        <div class="container-fluid">
          <div class="navbar-header">
            <a class="navbar-brand" href="#">
              <img alt="Brand" src="...">
            </a>
          </div>
        </div>
      </nav>
    
<!-- 상속할때 필요한 템플릿 언어---> 
    <div class="container">
        {% block content %}
        {% endblock %}
    </div>
    
</body>
</html>
```

```
home.html 상단에 
{% extends 'base.html%'} 
 {% block content %}

   (사이는 상속받을 내용을)
하단에
{% endblock %}
<!--상속 선언을 위해-->

settings.py_TEMPLATES 안 DIRS 안에
'lionproject/templates' 
< 경로 연결 >
```





- # **Path 관리**

:App별로 관련 urls.py 묶어서 관리를 위해

**관련 App폴더 내부에 urls.py를  생성.**

```
<blog_urls.py>
from django.contrib import admin
from django.urls import path

from .views import *
#blog.views -> .views 로 변경 위치 변경으로 인해 


urlpatterns = [
    #path('admin/', admin.site.urls), #삭제: 어디민도 프로젝트에서 관리
    #path('',mainPage,name='mainPage'), #삭제: 메인페이지는 프로젝트에서 관리
    #앱 관련 path만 남겨둔다
    path('<str:id>',detail,name='detail'), 
    path('new/',new,name='new'),
    path('create/',create,name="create"),
    path('edit/<str:id>',edit,name="edit"),
    path('update/<str:id>',update,name="update"),
    path('delete/<str:id>',delete,name="delete")
]
```

이렇게 변경해준다.

그 다음 프로젝트 폴더 내부의 urls.py는 이렇게 변경해준다.

```
<lionproject_urls.py>
from django.contrib import admin
from django.urls import path,(include)추가 
from blog.views impro
from blog.views import mainPage #mainPage만 불러오면 된다.

urlpatterns = [
    path('admin/', admin.site.urls),
    path('',mainPage,name='mainPage'), 
 	path('blog/',include('blog.urls'))
    #추가
]
```

다른 html.파일에도 동일하게 적용하면 네비게이션바가 동일하게 적용됨을 확인 할 수 있다..