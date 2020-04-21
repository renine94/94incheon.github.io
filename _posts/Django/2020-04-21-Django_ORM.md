---
# 게시글 맨위 사진
header:
  overlay_image: /assets/images/unsplash02.jpg
  # 카톡으로 url 보낼때 사진
  og_image: /assets/images/logo_Django.jpg
  # 출처
  caption: "출처 : [**Django CookBook**](https://django-orm-cookbook-ko.readthedocs.io/en/latest/index.html)"
  # 맨 아래 이미지
  teaser: /assets/images/logo_Django.jpg # 맨 아래 참고 이미지

# 게시글 제목/설명
title:  # "algorithm : 제목111"
excerpt: # "테스트내용입니다."

# 목차설정부분
toc: true
toc_sticky: true
toc_label: "페이지 주요 목차"

# 카테고리/태그
categories: Django
tags: python Django ORM QuerySet
---

# Django ORM

> [참고문헌 : Django ORM CookBook](https://django-orm-cookbook-ko.readthedocs.io/en/latest/index.html)



## QuerySet

### queryset 이란

이를 위해 빼놓을 수 없는 개념이 바로 `queryset` 입니다. `query` 란 데이터베이스에 정보를 요청해주는 것을 의미하며 파이썬으로 작성한 코드가 sql 로 매핑되어 `queryset` 이라는 자료 형태로 값이 넘어오게 됩니다. 이는 순회가능한 데이터로서 이를 이용하여 1개 이상의 데이터를 불러와 사용할 수 있습니다. `queryset` 의 특이한 점은 `Lazy` 한 특성입니다. 미리 db 에 접근해서 값을 불러오는 것이 아니라 출력 등과 같이 필요한 순간에 sql로 매핑되고 이를 통해 db에 접근해 값을 가져오게 됩니다.



### queryset 생성하기

`queryset` 에 대해서 알아보기 위해 model 하나를 정의하고 가도록 하겠습니다.

`1:N 관계`
```python
# core/models.py

from django.db import models

# 1
class Reporter(models.Model):
    username = models.CharField(max_length=30)
# N
class Article(models.Model):
    title = models.CharField(max_length=30)
    content = models.TextField()
    reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)
```

`queryset` 을 생성하기 위해서는 `ModelName.objects` 를 통해 가능합니다. 위의 `Article` 같은 경우에는 다음과 같습니다.

```
Article.objects
```



##### 다양한 조회 요청 방법

- **all**

  ```
  Article.objects.all()
  ```

  모든 데이터를 다 가져옵니다.

- **filter**

  ```
  Article.objects.filter()
  ```

  특정 데이터로 필터링해서 가져옵니다. 인자로는 `필드명=조건값` 이 들어가며 2개 이상 들어갈 경우, 두 조건을 and로 묶어주게 됩니다. or 로 묶어주기 위해서는 아래와 같이 Q 를 사용해야합니다.

  ```
  from django.db.models import Q

  Item.objects.filter(Q(title="제목") | Q(content="내용"))
  ```

- **exclude**

  ```
  Article.objects.exclude()
  ```

  filter 와 상반되는 개념으로, `필드명=조건값` 으로 들어오는 인자를 제외한 나머지 값들을 가져옵니다.

- **get**

  ```
  Article.objects.get()
  ```

  `필드명=조건값` 를 인자로 가지며, 해당 하는 데이터가 유일하게 존재해야 합니다. 0개이거나 2개 이상이면 에러가 발생합니다.

- **first**

  ```
  Article.objects.first()
  ```

  가장 첫번째 데이터를 가져옵니다.

- **last**

  ```
  Article.objects.last()
  ```

  가장 마지막 데이터를 가져옵니다.

- **index**, **slice**

  ```
  Article.objects[index]
  Article.objects[start:stop:step]
  ```

  python 의 list와 같이 인덱싱 및 슬라이싱이 가능합니다. 단, 음수가 들어갈 수 없습니다.



##### 조건을 통한 데이터 조회 방법

`필드명__조건 = 조건값` 을 `filter` 의 인자로 넘겨주줘 다음과 같은 방식으로 조건을 부여하여 조건에 부합되는 데이터를 조회할 수 있습니다. 위에서 정의한 모델에 맞게 예시를 통해 알아보겠습니다.

각 예시는 언더바를 **두 개** 씩 사용하고 있으므로 헷갈리지 맙시다.

- 숫자 / 날짜 /시간 필드

  - 필드명__lt

  ```
  Article.objects.filter(is_published__lt = date(1961,1,1))
  ```

  필드명__lt = 조건값 : 필드명 < 조건값

  - 필드명__lte

  필드명__lte = 조건값 : 필드명 <= 조건값

  - 필드명__gt

  필드명__gt = 조건값 : 필드명 < 조건값

  - 필드명__gte

  필드명__gte = 조건값 : 필드명 < 조건값



  예시는 전체적으로 비슷하다고 생각되어 첫번째 에서만 기재해주었습니다. 각 알파벳은 의미하는 함축어가 존재하는데, `l 은 less` 를 `g는 greater` 를`t 는 than` 을 `e 는 equal` 을 의미합니다.



- 문자열 필드

  - **필드명__startswith**

  ```
  Article.objects.filter(title__startswich="[Django]")
  ```

  `필드명__startswith = 조건값` 을 통해 조건값으로 시작하는 데이터를 모두 가져옵니다.

  - **필드명__endswith**

  `필드명__endswith = 조건값` 을 통해 조건값으로 끝나는 데이터를 모두 가져옵니다.

  - **필드명__contains**

  `필드명__contains = 조건값` 을 통해 조건값이 포함되는 데이터를 모두 가져옵니다.

  - **필드명__istartswith**

  `필드명__istartswith = 조건값` , startswith 와 동일하지만 대소문자를 구분하지 않습니다.

  - **필드명__iendswith**

  `필드명__iendswith = 조건값` , endswith 와 동일하지만 대소문자를 구분하지 않습니다.

  - **필드명__icontains**

  `필드명__icontains = 조건값`, contains 와 동일하지만 대소문자를 구분하지 않습니다.



##### 데이터 정렬

정렬 조건을 지정하는 방법으로는 2가지가 있습니다.

- **모델 클래스에서 지정**

```
# core/models.py

from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=30)
    content = models.TextField()
    is_published = models.BooleanField(default=False)

    class Meta:
      	ordering = ['id']
        # 역순으로 지정
        # ordering = ['-id']
```

Meta 클래스에 `ordering` 을 이용해서 list 로서 지정을 해줍니다.

클래스에 정렬을 추가하는건 모든 클래스에 다 권장 사항입니다. 데이터베이스가 자체적으로 최적화 하는 과정에서 순서가 바뀔 가능성이 있기 때문에 이를 통해 순서를 보장받을 수 있습니다.



- **queryset에서 지정**

```
Article.objects.all().order_by('id')
# 역순으로 지정
Article.objects.all().order_by('-id')
```

다음과 같이 `order_by` 를 통해서 해당 필드 순서로 `queryset`을 정렬합니다.

두 가지 방식을 동시에 하면 `queryset`에 `order_by` 지정 을 따릅니다.
