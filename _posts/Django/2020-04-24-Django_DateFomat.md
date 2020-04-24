---
# 게시글 맨위 사진
header:
  # 제목/내용 이미지
  overlay_image: /assets/images/unsplash02.jpg
  # 카톡으로 url 보낼때 사진
  og_image: /assets/images/logo_Django.jpg
  # 출처
  caption: "출처 : [**개인**](https://94incheon.github.io)"
  # 맨 아래 이미지
  teaser: /assets/images/logo_Django.jpg

# 게시글 제목/설명
title:  "Django : 장고 DTL 언어 - 날짜(Date) 출력, 저장하기"
excerpt: "출력형식을 지정하고, DB에 데이터 넣는 형식을 지정해서 넣자."

# 목차설정부분
toc: true
toc_sticky: true
toc_label: "페이지 주요 목차"

# 카테고리/태그
categories: Django
tags: formatting
---


# Django - DTL 날짜 출력

## 1. 날짜 표현

> [참고 문서](https://docs.python.org/3/library/datetime.html#strftime-strptime-behavior)



```django

{ { date|date:'표현식' } }

기준 시간 : 2020-04-24 14:10:01 (KST)

{ { date|date:'Y년 m월 d일' } }
2020년 04월 24일

```



- `a, A`
  - 오전 / 오후
- `m, M`
  - 월 숫자만  4 / 월 뒤에 '월' 글자 붙음 ( 4월 )
- `d`
  - 날만 나옴 ( 24 )

- `p`
  - 시 : 분 오전/오후 ( 02: 10 오후 )




## 2. DB에 원하는 포멧 저장

```
질문.
db에 저장할 때 원하는 포맷으로 저장하려면 어떻게 해야 하나요? (예를 들어서 '%Y/%m/%d %H:%M:%S)

답변.
t = forms.DateTimeField(input_formats=['%Y/%m/%d %H:%M:%S'])
```

