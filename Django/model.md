| 필드타입      | Html위젯      | 필수옵션    |
| ------------- | ------------- | ----------- |
| BooleanField  | Checkboxinput |             |
| CharField     | Textinput     | max_lenghth |
| DateField     | Textinput     |             |
| DateRimeField | Textinput     |             |
| FloatField    | Numberinput   |             |
| TextField     | Numberinput   |             |
| intergerFied  | Textarea      |             |

-  python manage.py makemigrations  : model.py의 변화를 감지해서  table을 이렇게 만들 것이다라는 것을 앱내의migration 폴더를 만들어서 models.py의변경사항 저장
- python manage.py  migrate : migration 폴더를 뒤져 실행시키고 데이터 베이스에 적용
- class Blog(models.Model): ID column 있음.

makemigra

```models.py
from django.db import models

# Create your models here.
class Blog(models.Model):
    title = models.CharField(max_length=200)  # 필드 검색 후 찾아서 쓰기
    writer = models.CharField(max_length=200)
    pub_date = models.DateTimeField()  # () 속성 넣는곳
    body =models.TextField()



```

python manage.py createsuperuser : admin계정 만들기

```admin.py
from .models import Blog
#.models.py에 Blog를 등록했다
admin.site.reister(Blog)
#블로그라는 테이블을 adin 사이트에 등록
```

```
    def __str__(self):
        return self.writer
        #title 내용을 이름으로
```

