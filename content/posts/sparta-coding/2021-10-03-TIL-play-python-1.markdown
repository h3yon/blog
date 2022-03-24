---
title: "[TIL] 힙한취미코딩! 파이썬 혼자놀기 패키지 1주차 개발일지"

toc: true
toc_sticky: true

categories:
  - Crawling
tags:
  - Crawling
---

## [TIL] 힙한취미코딩! 파이썬 혼자놀기 패키지 1주차 개발일지

스파르타코딩클럽에서  
파이썬 혼자놀기 패키지가 있길래 한번 들어보았다!  

## 오늘의 목표

아래처럼 연예인 사진을 검색해서  
저장해보는 게 오늘의 목표이다!!

<img src="https://user-images.githubusercontent.com/46602874/135744028-8b379b96-999c-4680-8123-fbc688edb731.png">

정리한 코드: [코드](https://github.com/h3yon/Crawling-TEST/commit/f4463fc9ab2ed13482d4dec119ededb9223648f7)

## 사용 모듈!

사용 모듈은 아래와 같다.

```
from bs4 import BeautifulSoup
from selenium import webdriver
import dload
import time
```

일단 chrome driver를 사용해서 검색을 하고,  
해당 페이지의 소스를 req에 담는다.  

<img src="https://user-images.githubusercontent.com/46602874/135744199-b1673d56-8940-409b-8901-7a9d3c576dbd.png">

사진처럼 div > a > img에 속하므로  
soup.select 안에 해당 경로를 넣어준다.

```
for thumbnail in thumbnails:
    src = thumbnail["src"]
    dload.save(src,f'img_homework/{i}.jpg')
    i += 1
```

thumbnails만큼 돌아서 내 디렉토리 안에 넣음을 확인했다~!

## 후기

좋아하는 배우 얼굴로 눈호강도 하고  
크롤링도 간단하게 접해볼 수 있어서 너무 좋았다!