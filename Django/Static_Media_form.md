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
    image = models.ImageField(upload_to='blog/', blank=True, null=True)
    #upload_to="blog/" : 업로드할 폴더를 지정하는 것, settings.py에서 MEDIA_URL로 지정해둔 media 폴더안에 blog 폴더를 만들어서 관리하겠다는 의미
    #blank와 null : 빈값 에러 방지 (비어있을수도 있다고 알림)
    def __str__(self): 
        return self.title 
    def summary(self):
        return self.body[:100] 
```



### next   // migration 파일 삭제후 다시 선언 

```
pip install pillow(터미널)
#이미지 파일들을 관리하기 위한 별도의 모듈 설치
->admin 접속시 어드민 패널에 이미지 파일 업로드란 생성
```

```
< blog_new.html>
  사진: <input type="file" name="image">
    <!--사진 받는 칸 추가--->
```

```
def create(request):
    new_blog = Blog()
    new_blog.title = request.POST['title'] 
    new_blog.writer = request.POST['writer']
    new_blog.body = request.POST['body']
    new_blog.image = request.FILES['image'] #추가 new
    new_blog.pub_date = timezone.now()
    new_blog.save()
    return redirect('detail',new_blog.id)
```

```
<detail.html>
<img src="{{blogTable.image.url}}" alt=""> <!--추가 이미지 경로를 보여주므로 디테일 페이지에서 사진을 볼 수 있음-->
```

# **Form**

------

테이블의 칼럼의 데이터 즉 입력받는 데이터들의 형식을 관리하기위함

new에서 원래는 input 테그를 통해 데이터를 받고 create에서 데이터테이블 객체를 새로 생성 그 데이터를 저장하는 방식을 사용하였으나

form을 이용하면 input 테그 없이 입력창을 구현하고 create에서 별도 객체 생성없이 바로 저장가능

또한 전체 폼에 대하여 한번에 관리하기 편해짐



```
<Blog앱에 forms.py를 생성>
<Blog_forms.py>

from django import forms
from .models import Blog  #테이블을 사용하기 위해 import

#폼관리할 테이블의 클래스
clsaa BlogForm(forms.ModelForm):
    class Meta:
        model = Blog #테이블
        fields = ['title','writer','body','images'] #폼관리 필드정보    
```



```
<views.py>
#new.html 적용
rom .forms import BlogForm #입력받는 공간


def new(request): 
    form = BlogForm() #폼 객체 호출
    return render(request,'new.html',{'form':form}) #매개변수로 보내주기 to new.html
```



```
<!--기존--->
<h1>글을 작성해주시요</h1>

<form action="{%url 'create'%}" method="post" enctype="multipart/form-data"> 
    {%csrf_token%}
    제목: <input type="text" name="title">
    작가: <input type="text" name="writer">
    사진: <input type="file" name="images">
    내용:<textarea name="body" id="" cols="30" rows="10"></textarea>
    <button type="submit">저장</button>
</form>

<!--변경후--->
<h1>글을 작성해주시요</h1>

<form action="{%url 'create'%}" method="post" enctype="multipart/form-data">
    {%csrf_token%}
    {{form}} <!--폼-->
    <!--{{form.as_p}}-->
    <button type="submit">저장</button>
</form>
 **new.html 기존 input 박스들을 다 지우고 form을 받아온다.**

 **.as_p는 form의요소들을 p 테그로 묶어준다는 의미이다.**
```

```
<views.py>
create함수에서
form = BlogForm(reuqeest.POST,request.FILES)
	if form.is_valid():
        new_blog=form.save(commit=False)
        new_blog.put_date= timezone.now()
        new_blog.save()
        return redirect('detail', new_blog.id)
        #임시저장 이폼이 만약 모델에 있는 
	return redirect('home')

```