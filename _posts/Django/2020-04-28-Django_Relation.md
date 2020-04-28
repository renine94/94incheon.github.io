---
# 게시글 맨위 사진
header:
  # 제목/내용 이미지
  overlay_image: /assets/images/unsplash02.jpg
  # 카톡으로 url 보낼때 사진
  og_image: /assets/images/logo_Django.jpg
  # 출처
  caption: "출처 : [**개인**](https://www.github.com/94incheon)"
  # 맨 아래 이미지
  teaser: /assets/images/logo_Django.jpg

# 게시글 제목/설명
title:  "Django : 장고 Table 관계설정 1:N, M:N"
excerpt: "장고를 이용하여 모델을 정의할때 테이블 관계를 설정하는법을 알아보자."

# 목차설정부분
toc: true
toc_sticky: true
toc_label: "페이지 주요 목차"

# 카테고리/태그
categories: Django
tags: Relation Table
---

# 모델링 (테이블 설정) 관계

## 1. 1:1 관계


## 2. 1:N 관계

> ex) 게시글과 댓글

### 기본설정하기

### Comment 모델 클래스 생성

- 어떤 유저가 쓴 댓글인지 & 어떠한 게시글에 달린 댓글인지에 대한 1:N 관계 설정

  - 클래스는 posts 어플리케이션 내에 작성

- ```plaintext
  models.ForeignKey
  ```



  의 첫번째 인자로는 외래키가 연결되는 테이블을 입력함.

  - `settings.AUTH_USER_MODEL` 에는 유저 테이블에 대한 정보가 담겨 있음.

- 두번째 인자에는



  ```plaintext
  ForeignKeyField
  ```



  가 바라보는 값이 삭제될 때 어떻게 처리할지를 옵션으로 정함

  - `CASCADE` : 부모가 삭제 되면, 자기 자신도 삭제
  - `PROTECT` : 자식이 존재하면, 부모 삭제 불가능 ( `ProtectedError `발생시킴)
  - `SET_NULL` : 부모가 삭제되면, 자식의 부모 정보를 NULL로 변경

```python
from django.conf import settings

class Comment(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    content = models.TextField()
```

- 스키마 재설정(`makemigrations` & `migrate` )

### Comment 모델에 대한 모델폼 생성

```python
from django import forms
from .models import Comment

class CommentForm(forms.ModelForm):
    model = Comment
    fields = ['content',]
```

### 댓글 생성 및 읽기(Create & Read)

### 댓글 생성하기

- models.py & forms.py 내 새로 생성한 클래스인 `Comment` 와 `CommentForm`을 각각 import

- 앞 포스팅에서 views.py - create 함수를 통해 게시글을 생성할 때, 유저정보를 입력하였던 방법을 동일하게 호활용하여, Comment - CommentForm 으로 wrapping 되어있는 데이터를 한번 벗겨 낸 후, comment의 인스턴스 객체의 멤버변수인 user 과 post에 각각 데이터를 집어넣음.

  - `comment`의 멤버변수인 `post` 는 `Post` 모델과 외래키로 연결되어있으며, 이때 자동적으로 연결된 `post`에 대한 id를 입력하는 column이 생성된다. `Comment` 모델 클래스를 보면 `post` 필드라고 명시 되어있지만 실제로 생성되는 필드는 `post`가 아니라 `post_id` 이다. (Default 값)

- ```plaintext
  comment.post_id
  ```



  에서



  ```plaintext
  post_id
  ```



  는 함수의 2번째 인자인



  ```plaintext
  post_id
  ```



  와는 무관함을 유의하자.

  - 함수의 2번째 인자로 들어오는 `post_id` 는 urls.py를 통해 들어오는 variable routing을 의미.
  - `comment.post_id` 는 `comment` 모델에서 외래키로 연결된 `post`의 id 값을 저장시키는 필드명을 나타냄.

```python
#views.py
from .models import Comment
from .forms import CommentForm

def comment_create(request, post_id):
    comment_form = CommentForm(request.POST)
    if comment_form.is_valid():
        comment = Comment_form.save(commit=False)
        comment.user = request.user
        comment.post_id = post_id
        comment.save()
    return redirect('posts:list')

#urls.py
urlpatterns = [
    path('<int:comment_id>/comments/create/', views.comment_create, name='comment_create'),
]
```

- 위의 코드는 아래와 같은 방법으로도 나타낼 수 있음. 그러나 DB를 접속하여 어떠한 게시글인지에 대한 정보를 한번 불러오는 등에 불필요한 과정이 포함되어있으므로 비효율적임.

```python
def comment_create(request, post_id):
    #DB에서부터 어떠한 게시글인지에대한 정보를 찾아서 post변수에 저장
    post = get_object_or_404(Post, id=post_id)
    comment_form = CommentForm(request.POST)
    if comment_form.is_valid():
        comment = comment_form.save(commit=False)
        comment.user = request.user
        #comment.post를 통해 comment에 대한 1:N 관계 설정
        comment.post = post
        comment.save()

    return redirect('posts:list')
```

### HTTP methods에 대한 데코레이터 설정

이전 포스팅에서 `@login_required` 데코레이터를 사용하여 로그인이 되어야만이 함수가 실행되게끔 설정을하였던 것처럼, HTTP methods에 대한 데코레이터 설정이 가능함. 해당 데코레이터 사용을 위해 관련코드 import`

- `from django.views.decorators.http import require_POST`
- import 이하에는 원하는 HTTP methods를 입력해주면 됨
  - `require_POST`: POST 방식으로 주소를 접근할때만 함수를 실행하게 설정
  - `require_GET` : GET 방식만 가능
  - `require_safe` :GET 방식과 HEAD방식만 가능
  - `require_http_methods(['GET','POST'])` : GET, POST방식 사용 가능
- 위의 방법을 사용하면 `if request.method == 'POST'` 와 같은 조건문을 사용할 필요가 없음
- [공식문서](https://docs.djangoproject.com/en/2.2/topics/http/decorators/) 참조

```
from django.views.decorators.http import require_POST

@require_POST
def comment_create(request, post_id):
    comment_form = CommentForm(request.POST)
    if comment_form.is_valid():
        comment = comment_form.save(commit=False)
        comment.user = request.user
        comment.post_id = post_id
        comment.save()

    return redirect('posts:list')
```

### 댓글 표시하기(views.py)

- 리스트 페이지에 댓글을 보여주기 위해 list 함수 수정
- forms.py 내 생성한 `CommentForm` 을 사용하기 위해 불러온 후, 템플릿 변수로 넘겨줌
- 추가로, 최근에 작성한 게시글이 상위에 나타내기 위해 `order_by('-id')` 입력

```
def list(request):
    posts = Post.objects.order_by('-id').all()
    comment_form = CommentForm()
    return render(request, 'posts/list.html', {'posts':posts, 'comment_form': comment_form})
```

### 댓글 표시 & 생성(_post.html)

- 댓글 정보 및 생성기능을 실제로 브라우져에 나타내기 위한 코드를 `_post.html` 에 작성
- urls.py 에 작성된 url 경로에 따라 함수가 실행될 수 있도록 form 태그 및 action 속성 경로 설정
  - `{% if user.is_authenticated %}` 을 사용하여 로그인이 된 경우만 댓글생성이 보이도록 함.
- 댓글 목록도 동시에 출력 (부트스트랩 레이아웃 적용)
  - ’`{% for comment in post.comment_set.all %}` 진자템플릿을 이용하여 반복문을 실행함.
  - 실제 코드는 `post.comment_set.all()` 으로 작성되어야 하나, 진자템플릿 문법에는 `()` 이 생략됨.
  - 반복문이 비어있을 경우, `{% empty %}` 이하 코드 실행됨.

※ Post와 Comment는 models.py를 통해 1:N 관계가 설정되어있으며, 각각의 위치에서 관계된 다른반대쪽을 불러오는 방법은 아래와 같음

- Post에서 Comment로 접근할 때(1 => N) : `post.comment.set.all()`
- Comment에서 Post로 접근할 때(N => 1) : `comment.post`

```
<!-- _post.html -->

<div class="card-body">
    {% for comment in post.comment_set.all %}
      <div class="card-text">
        <strong> {{ comment.user.username }} </strong> {{ comment.content }}
      </div>
    {% empty %}
      <div class="card-text">댓글이 없습니다.</div>
    {% endfor %}
</div>

{% if user.is_authenticated %}
<form action="{% url 'posts:comment_create' post.id %}" method="POST">
   {% csrf_token %}
   {{ comment_form }}
   <input type="submit" value="Submit"/>
</form>
{% endif %}
```

### 모델 필드속성 오버라이딩 설정

Model은 실제 DB에 저장 시키기 위해 사용(스키마의 형태)하는 역할을 함. `Comment` 모델의 `content` 필드의 경우 `TextField`를 사용하였으나, 실제 댓글은 길이의 제한이 없는 `TextField` 보다는 길이제한을 둘 수 있는 `CharField`가 좀 더 적합하다. 따라서 `content`의 필드 속성을 `TextField `에서 `CharField` 로 변경하는 작업을 forms.py에서 할 수 있음.(https://docs.djangoproject.com/en/2.2/topics/forms/modelforms/#overriding-the-default-fields)

- `label` : 기본적인 input의 이름. 빈 값을 넣어서 라벨을 제거.

- ```plaintext
  widget
  ```



  : 속성값을 줄 때 사용.



  ```plaintext
  widget
  ```

  의 속성을



  ```plaintext
  forms.TextInput
  ```



  이나



  ```plaintext
  forms.Textarea
  ```



  등 형태를 설정할 수 있음.

  - `TextInput` 의 속성을 괄호안에 사용. `attrs={}` 와 같이 딕셔너리의 형태로 입력 가능.

```python
#forms.py

class CommentForm(forms.ModelForm):
    content = forms.CharField(label='', widget=forms.TextInput({attrs={'class':'form-control', 'placeholder': '댓글을 작성하세요.'}}))
    class Meta:
        model = Comment
        fields = ['content',]
```

- 아래와 같은 방법으로도 작성 가능함

```python
class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['content']
        widgets = {
            'content': forms.TextInput(attrs={'placeholder':'댓글을 입력하세요'})
        }
```

### 댓글 생성 폼에 bootstrap 입히기 (_post.html)

```html
{% if user.is_authenticated %}
<form action="{% url 'posts:comment_create' post.id %}" method="POST">
    {% csrf_token %}
    <div class="input-group">
        {{ comment_form }}
        <div class="input-group-append">
            <input type="submit" value="Submit" class="btn btn-primary"/>
        </div>
    </div>
</form>
{% endif %}
```


## 3. M:N 관계

> ex) 유저와 게시글 좋아요수, 의사와 환자 등등.

```python
# models.py

class Doctor(models.Model):
    name = models.CharField(max_length=10)

class Patient(models.Model):
    name = models.CharField(max_length=10)
    doctors = models.ManyToManyField(Doctor, related_name='patients')

# 중계테이블이 필요한 경우
# 1. 예약 날짜와 같은 컬럼을 추가로 만들 필요가 있을때,
# 2. id만 저장할거면 중계테이블은 필요없음 (ManyToManyField 가 알아서 만들어 준다.)
#class Reservation(models.Model):
    #doctor = models.ForeignKey(Doctor, on_delete=Models.CASCADE)
    #patient = models.ForeignKey(Patient, on_delete=Models.CASCADE)
    #date = models.DateTimeField(auto_now_add=True)

```

![image-20200428101855379](/assets/images/Django/image-20200428101855379.png)


- 동일한모델에서 같은 테이블(User) 을 외래키로 잡아버리면, `역참조 네임` 을 지정해줘야 할 경우가 발생한다.

```python
class Article(models.Model):
    title =
    content =
    created_at =
    updated_at =
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    # articles_article_like_user 테이블 생성
    # 목적어가 되는쪽에 작성해준다. ex) 유저는 [게시글] 을 좋아함.
    like_users = models.ManyToManyField(settings.AUTH_USER_MODEL,
                                       related_name='like_article')

class Comment(models.Model):
    pass
```



### 3.1 좋아요 기능

> url -> variable routing
> view -> 좋아요 눌렀으면 취소, 안눌렀으면 좋아요.
> Template -> redirect

```python
# urls.py
path('<int:pk>/like/', views.like, name='like')

# views.py
def like(request, pk):
    article = get_object_or_404(Article, pk=pk)
    # 좋아요를 누른적이 있다면, => DB에 저장되어 있으면
    if request.user in article.like_users.all():
    # if article.like_users.filter(id=request.user.pk).exists():
        # 좋아요 취소
        article.like_users.remove(request.user)
    else:
        # 좋아요
        article.like_users.add(request.user)
    return redirect('articles:detail', article.pk)
```
