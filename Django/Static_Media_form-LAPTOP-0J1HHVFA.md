# Static

1. 정적파일: 서버에 미리 업로드된 파일
2. 동적파일: 서버의 데이터들이 가공된다음 보여지는 파일



- ### **정적파일**

------

1. Static:  img js css 등 개발자가 서버 개발시 미리 넣어놓는 파일
2. Media: 사용자가 업로드 할수있는 파일



## 1.Static 파일 추가

> static 파일을 개발단계에서 심기

#### **1.static 파일 관리**

APP폴더에  static 폴더 생성 후  PNG파일 넣기

settings하단에 추가

```
<lionproject_settings.py>


STATIC_URL = '/static/'

#현재 static 파일들이 어디에 있는지 
STATIC_DIRS = [
     os.path.join(BASE_DIR,'blog','static')
]
STATIC_ROOT=os.path.join(BASE_DIR,'static')
#static 파일을 어디에 모을건지

```



```
파일을 모으기 위한 명령어  (터미널)
python manage.py collectstatic
static 폴더가 생성되고 static 파일들이 모임.
```



**정적 파일을 편리하게 프로젝트 폴더에서 관리할수있다.**

## 2.staic 파일 적용

```
<base.html>

<body>
    {% load static %} 
    <!--static 파일 로드--->
    
    
    <img src="{%static 'likelion.PNG' %}" 
    alt="" width="50" height="50">  
    <!--static파일 적용--->
</body>
```





- # Media

------

: 사용자가  파일 업로드 ->  파일을 서버가 저장 -> 요청시 제공

웹통신 방식은 url이므로 사진 url로 보여준다.

```
<lionproject_settings.py>

MEDIA_ROOT = os.path.join(BASE_DIR,'static')
#이용자가 업로드한 파일을 모으는 곳

MEDIA_URL = '/media/'
#media파일에 url을 설정    -> 웹은 url로 통신
```



### next

```
<lionproject_urls.py>

from django.conf import settings
from django.conf.urls.static import static
#추가

urlpatterns =[
   
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
#settings.py의 MEDIA URL, MEDIA ROOT 정보를 받는다고 생각.
#한번 설정시 계속 사용 가능
```



### next

```
<blog.models.py>
# 테이블 내부에 이미지들을 저장할 칼럼을 만들어준다.

#블로그(게시물) 테이블
class Blog(models.Model):
    
    title = models.CharField(max_length=200)
    writer = models.CharField(max_length=100)
    pub_date = models.DateTimeField()
    body = models.TextField()
    
    #upload_to="blog/" : 업로드할 폴더를 지정하는 것, settings.py에서 MEDIA_URL로 지정해둔 media 폴더안에 blog 폴더를 	#만들어서 관리하겠다는 의미
    #blank와 null 설정으로 빈값 에러를 방지
    image = models.ImageField(upload_to='blog/', blank=True, null=True)

    def __str__(self): 
        return self.title 
    
    def summary(self):
        return self.body[:100] 
```

image를 선언하여 media 파일들을 관리할 준비를 한다.

(이 단계에서 만약 기존 data 들이 존재한다면 데 새 칼럼이 추가된 경우 임으로 기존 데이터들이 error를 반환한다. 해결법은 차차 찾아보자.)

이미지 파일들을 잘 관리하기 위해 별도의 모듈을 설치한다.

```
pip install pillow
```

run server를 해보면 admin page에는 이미지 파일 업로드란이 생겼다.

이제 일반 페이지 new.html에서 글 생성시 이미지 파일이 생성되도록 해보자

```
<!--In new.html-->
<h1>글을 작성해주시요</h1>

<form action="{%url 'create'%}" method="post" enctype="multipart/form-data"> <!--enctype="multipart/form-data" 을 추가하여 image를 넘겨준다-->
    {%csrf_token%}
    제목: <input type="text" name="title">
    작가: <input type="text" name="writer">
    사진: <input type="file" name="image">
    <!--사진 받는 칸 추가-->
    내용:<textarea name="body" id="" cols="30" rows="10"></textarea>
    <button type="submit">저장</button>
</form>
```

그다음 create 메소드 in views.py에 image data를 포함하여 객체 생성한다.

```
def create(request):
    new_blog = Blog()
    new_blog.title = request.POST['title'] 
    new_blog.writer = request.POST['writer']
    new_blog.body = request.POST['body']
    new_blog.image = request.FILES['image'] #추가 new.html에서 이름을 image라고 했기때문에
    new_blog.pub_date = timezone.now()
    new_blog.save()
    return redirect('detail',new_blog.id)
```

생성했고 객체로 데이터도 저장했으면 실제 화면에 보이도록 해야한다.

Media 파일은 서버에 저장되어있고 사용자에겐 url을 통해 보여주기 때문에 url을 이용한다.

상세페이지 detail로 이동한다.

```
<!--In detail.html-->
<img src="{{blogTable.image.url}}" alt="">
```

이미지를 화면에 노출시킨다.

.models.py의 만들어준 image 칼럼엔 이미지 자체 data가 아니라 image의 url을 저장해두기 때문에 객체명.image.url로 참조한다.

------

------

# **Form**

------

테이블의 칼럼의 데이터 즉 입력받는 데이터들의 형식을 관리하기위함

new에서 원래는 input 테그를 통해 데이터를 받고 create에서 데이터테이블 객체를 새로 생성 그 데이터를 저장하는 방식을 사용하였으나

form을 이용하면 input 테그 없이 입력창을 구현하고 create에서 별도 객체 생성없이 바로 저장가능

또한 전체 폼에 대하여 한번에 관리하기 편해짐

App에 forms.py를 생성

```
#In App.forms.py
from django import forms
from .models import Blog #테이블 가져옴

#폼관리할 테이블의 클래스
clsaa BlogForm(forms.ModelForm):
    #메타클래스 작성
    class Meta:
        model = Blog #테이블
        fields = ['title','writer','body','images'] #폼관리할 필드정보    
```

이후 views.py로 이동한다, new.html에 폼을 적용시켜 보자

```
from .forms import BlogForm  #선언


def new(request): 
    form = BlogForm() #폼 객체 호출
    return render(request,'new.html',{'form':form}) #매개변수로 보내주기 to new.html
```

이제 new에서 기존의 input 박스들을 다 지우고 form을 받아온다.

아래 .as_p는 form의요소들을 p 테그로 묶어준다는 의미이다.

```
<!--변경전--------------------------------------------------------------------->
<h1>글을 작성해주시요</h1>

<form action="{%url 'create'%}" method="post" enctype="multipart/form-data"> 
    {%csrf_token%}
    제목: <input type="text" name="title">
    작가: <input type="text" name="writer">
    사진: <input type="file" name="images">
    내용:<textarea name="body" id="" cols="30" rows="10"></textarea>
    <button type="submit">저장</button>
</form>

<!--변경후--------------------------------------------------------------------->
<h1>글을 작성해주시요</h1>

<form action="{%url 'create'%}" method="post" enctype="multipart/form-data">
    {%csrf_token%}
    {{form}} <!--폼-->
    <!--{{form.as_p}}-->
    <button type="submit">저장</button>
</form>
```

{{form}}위치에 .models의 칼럼들이 위치하게 되어 입력을 받을수있게된다.

이렇게 입력을 관리할수있지만 이 외에도 입력을 저장할때도 활용된다,

```
#views.create
#new.html에서 action을 통해 create 메소드로 정보를 보내 database에 저장했었다.

#=변경전==========================================================================
def create(request):
    new_blog = Blog() #in .models의 객체

    new_blog.title = request.POST['title'] #from new.html action
    new_blog.writer = request.POST['writer']
    new_blog.body = request.POST['body']
    new_blog.image = request.FILES['images']
    new_blog.pub_date = timezone.now()
    new_blog.save()
    return redirect('detail',new_blog.id)


#=변경후==========================================================================

#이걸 form을 통해 저장하도록 할수있다.
def create(request):
  	form = BlogForm(request.POST,request.FILES)
    if form.is_valid(): #폼이 유요한가?
        new_blog = form.save(commit=False) #임시저장
        new_blog.pub_date = timezone.now() #form에서 pub_date는 관리하지 않았기 때문에 별로도 저장해줘야함
        new_blog.save()  
        return redirect('detail',new_blog.id)
    return rediect('home')
```