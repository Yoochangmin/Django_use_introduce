# Read ---

---

blog  -> template폴더 ->home.html

```Blog.views.py 
(Blog.views.py )
from .models import Bolg

def home(request):
	blogs = Blog.objects.all() //블로그에 있는 객체들을 전부 가져온다
	return render(request, 'home.html',{'bolgs:blog'})
```

------

```
lionproject.urls.py 등록
from blog.views import *  //blog앱에 있는 views 파일을 가져옴 
*:내부 모든것 가져옴

urlpatterns = [
    path('admin/', admin.site.urls),
    path('',home,name="home")
]

```

```
(blog.home.html)

<!DOCYTPE html>
<html lang ="en">
<head>
    <meta charset= "UTF-8">
    <meta http=equiv ="X-UA-Compatible" content ="IE=edge"
    <meta name="viewpoint" content="width=device-width, initial-scale=1.0"
    <title>BLOG</title>
    <style>

    
</head>
<body>
    <h1>Blog Project</h1>
    {% for blog in blogs%}
        {{blog.title}}
        {{blog.writer}}
        {{blog.body}}#대신
        {{blog.summary}} #이렇게
    {% endfor %}
</body>
</html>
```

```
(blog.models.py 추가)
  def summary(self):
      return self.body[:100]  #본문을 100자로 잘라 보여줌
```



- ## path-converter : 디테일 페이지

---

```
(blog.vies.py 추가)
def deatail(request,blog_id):
       # blog =Blog.objects.get(id=id) 이렇게해도되지만
       blog = get_object_or_404(Blog, pk=id)  #pk :primary key 데이터베이스에서 식별자로 쓰기위한 id 값
       #id값이 있는 blog값을 가져오거나 404띄운다
       return render(request, 'detail.html',{'blog':blog})
```

```
(lionproject.urls.py 추가)


urlpatterns = [
  #  path('admin/', admin.site.urls),
  #  path('',home,name="home"),
    path('<str:id>',detail,name="detail")
    #데이터베이스의 id값에 따라 or 매개변수로
]

```

```
(blog.home.html 추가)

{{blog.summary}} <a href ="{%url 'detail' blog.id %}">...more</a>
```



```
(blog.template폴더.detail.html)  
<!DOCYTPE html>
<html lang ="en">
<head>
    <meta charset= "UTF-8">
    <meta http=equiv ="X-UA-Compatible" content ="IE=edge"
    <meta name="viewpoint" content="width=device-width, initial-scale=1.0"
    <title>BLOG</title>
    <style>


</head>
<body>
    <h1>Blog Project</h1>
    {% for blog in blogs%}
        {{blog.title}}
        {{blog.writer}}
       {% comment %} {{blog.body}} {% endcomment %}
       {{blog.summary}} <a href ="{%url 'detail' blog.id %}">...more</a>
    {% endfor %}
</body>
</html>
```

# Create

---

new: new.html 보여줌

creat: 데이터베이스에 저장

```
blog.views.py추가
def new(request):
       return render(request, 'new.html')
```

```
lionproject.urls.py 추가
 path('new/' ,new,name="new"),
```

```
blog.home.html
<body>
    <h1>Blog Project</h1>
    --<div>
    추가 --   <a href= "{%url 'new'%}"> write blog</a>
   -- </div>
    {% for blog in blogs%}
        {{blog.title}}
        {{blog.writer}}
       {% comment %} {{blog.body}} {% endcomment %}
       {{blog.summary}} <a href ="{%url 'detail' blog.id %}">...more</a>
    {% endfor %}
</body>
```

```
blog.new.html 
<h1>Write Your Blog</h1>

<form actiom="{%url 'create'%}"#create연결 method="post">
    {%csrf_token%}
   <p>제목:<input type="text" name="title"></p>
   <p>작성자: <input type="text" name="writer"></p>
    본문: <textarea name="body" id="" cols="30" rows="10"></textarea>
    <button type="submit"> submit</button>
</form>
```

```
blog.views.py추가
from django.utils import timezone#시간저장


def create(request):
       new_blog = Blog()
       
       new_blog.title =request.POST['title']
       new_blog.writer =request.POST['writer']
       new_blog.body =request.POST['body']
       new.blog.save()
       return redirect('detail', new_blog.id)
```

```
lionproject.urls.py 추가
path('create/',create,name="create"),
```

Get : 데이터를 얻ㄷ기 위한 요청 데이터가 url에 보임

Post : 데이터를 생성하기 위한 요청, 데이터 url안보임 

# Update

---

edit: edit.html 보여줌

update:데이터베이스에 적용

수정할 데이터의 id값을 받아야함  path converter 이용

```
blog.views.py 추가
def edit(request):
       edit_blog = Blog.objects.get(id=id)  # 객체하나를 가져옴
       return render(requeest, 'edit.html',{'blog':edit_blog})
       
```

```
lionproject urls.py 추가
앱 사용을 위해
path('edit/<str:id',edit, name="edit"),
```

```
templates.detail.html 추가

    <h1>{{blog.title}}</h1>
    작성자:{{blog.writer}}
    <br>
    날짜:{{blog.pub_date}}
    <hr>
    <p>{{blog.body}}</p>
    -- 추가--<a href="{% url 'edit' blog.id%}"></a> --
```

```
blog.edit.html 생성
<h1>Upate Your Blog</h1>

<form actiom="{%url 'edit' blog.id%}" method="post">  #blog.id 안쓰면 urls.py 에러발생 패스컨버터가 쓰이는 접근할때는 매개변수 전해야함
    {%csrf_token%} //필수
   <p>제목:<input type="text" name="title" value ="{{blog.title}}"></p>
   <p>작성자: <input type="text" name="writer" value = "{{blog.writer}}"></p>
    본문: <textarea name="body" id="" cols="30" rows="10">{{blog.body}}</textarea>
    <button type="submit"> submit</button>
</form>

```

```
 path('update/<str:id',update, name ="update"),
```

패스컨버터  <str:id>   



# Delete

---



```
view.py
def delete(request,id):
       delete_blog = Blog.objects.get(id=id)
       delete_blog.delete() #삭제 메소드
       return redirect('home')
```

```
urls.py
path('delete/str:Lid',delete, name="delete")
```

```
detail.html
<a href="{% url 'delete' blog.id%}"></a>
```

