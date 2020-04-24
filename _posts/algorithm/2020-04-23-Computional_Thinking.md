---
# 게시글 맨위 사진
header:
  # 제목/내용 이미지
  overlay_image: /assets/images/unsplash02.jpg
  # 카톡으로 url 보낼때 사진
  og_image: /assets/images/logo_Algorithm.jpg
  # 출처
  caption: "출처 : [**개인**](https://94incheon.github.io)"
  # 맨 아래 이미지
  teaser: /assets/images/logo_Algorithm.jpg

# 게시글 제목/설명
title:  "Algorithm : Computational Thinking - 컴퓨팅적 사고"
excerpt: "논리연산자 와 수의 표현 알아보기"

# 목차설정부분
toc: true
toc_sticky: true
toc_label: "페이지 주요 목차"

# 카테고리/태그
categories: Algorithm
tags: Algorithm
---
# Computational Thinking

## 1. 논리곱and ( p^q )

| p    | q    | p ^ q |
| ---- | ---- | ----- |
| T    | T    | T     |
| T    | F    | F     |
| F    | T    | F     |
| F    | F    | F     |



## 2. 논리합or ( pVq )

- p, q가 명제일 때, p, q 모두 거짓일 때만 거짓이 되는 명제
- p V q ( p or q, p 또는 q)

| P    | q    | p V q |
| ---- | ---- | ----- |
| T    | T    | T     |
| T    | F    | T     |
| F    | T    | T     |
| F    | F    | F     |



## 3. 조건 명제

- p, q가 명제일 때, 명제 p가 조건(또는 원인), q가 결론(또는 결과)로 제시되는 명제
- p => q ( p이면 q이다. )

| p    | q    | p => q |
| ---- | ---- | ------ |
| T    | T    | T      |
| T    | F    | F      |
| F    | T    | T      |
| F    | F    | T      |

### 3.1. 조건명제의 역, 이, 대우

- 역 : q => p
- 이 : ~p => ~ q
- 대우 : ~q => ~p

| p    | q    | p => q | q => p | ~p => ~q          | ~q => ~p  |
| ---- | ---- | ------ | ------ | ----------------- | --------- |
| T    | T    | T      | T      | F          F => T | F, F => T |
| T    | F    | F      | T      | F          T => T | T, F => T |
| F    | T    | T      | F      | T          F => F | F, T => T |
| F    | F    | T      | T      | T          T => T | T, T => T |





## 4.쌍방 조건명제

- p, q가 명제일 때, 명제 p와 q가 모두 조건이면서 결론인 명제
- p <=> q (p면 q고, q면 q다.)

| p    | q    | p <=> q |
| ---- | ---- | ------- |
| T    | T    | T       |
| T    | F    | F       |
| F    | T    | F       |
| F    | F    | T       |



## 5.명제식 진리표 만들기

### 5.1

1. p ^ (q => ~p)

| p    | q    | ~p   | q => ~p | p^(q => ~p) |
| ---- | ---- | ---- | ------- | ----------- |
| T    | T    | F    | F       | F           |
| T    | F    | F    | T       | T           |
| F    | T    | T    | T       | F           |
| F    | F    | T    | T       | F           |



# 수와 표현

`log N` 이란

- 2의 몇 승이 n이 되느냐
- n 을 표현하는 데 몇 비트가 필요한가의 답
- 1로 시작해서 계속 두 배를 할때 몇번 하면 n 이 되느냐의 답
- n 을 2로 계속 나눌 때 몇번 나누면 거의 1이 되느냐에 대한 답
