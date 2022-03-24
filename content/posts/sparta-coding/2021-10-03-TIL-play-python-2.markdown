---
title: "[TIL] 힙한취미코딩! 파이썬 혼자놀기 패키지 2주차 개발일지"

toc: true
toc_sticky: true

categories:
  - Crawling
tags:
  - Crawling
---

## [TIL] 힙한취미코딩! 파이썬 혼자놀기 패키지 2주차 개발일지

스파르타코딩클럽에서  
파이썬 혼자놀기 패키지가 있어서 2주차를 들어보았다! 

## 오늘의 목표

아래처럼 썸네일을 포함해 
기사를 크롤링해서 자신에게 메일 보내기!

- 정리한 코드: [코드](https://github.com/h3yon/Crawling-TEST/commit/8c01f5f86e3a491f3f7395c9b8537b89cf0358a0)

## 사용 모듈!

사용 모듈은 아래와 같다.

```py
# 이메일 관련 모듈
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email.mime.text import MIMEText
from email import encoders

# 크롤링 및 엑셀 변환 모듈
from openpyxl import Workbook
from bs4 import BeautifulSoup
from selenium import webdriver
```

모듈의 쓰임을 간단하게 살펴보면,  
이메일 관련이기 때문에 smtplib를 사용하는 걸 알 수 있고   

openpyxl은 py -> xlsx 관련 모듈임을 알 수 있다.
webdriver과 BeautifulSoup을 통해 크롤링함을 알 수 있다.

## 방법

### 엑셀 파일 만들어보기!

<img src="https://user-images.githubusercontent.com/46602874/135744342-3d75a703-2063-4d05-b718-b7274405184f.png">

네이버로 추석을 검색하고  
그 부분의 코드를 보면 경로를 알 수 있다.

```py
articles = soup.select('#main_pack > section.sc_new.sp_nnews._prs_nws > div > div.group_news > ul > li')

wb = Workbook()
ws1 = wb.active
ws1.title = "articles"
ws1.append(["제목", "링크", "신문사"])

for article in articles:
    title = article.select_one('div.news_area > a').text
    url = article.select_one('div.news_area > a')['href']
    comp = article.select_one('a.info.press').text.split(' ')[0].replace('언론사', '')

    ws1.append([title, url, comp, thumbnail])

driver.quit()
wb.save(filename='articles.xlsx')
```

그래서 엑셀 부분 컬럼명을 '["제목", "링크", "신문사", "썸네일"]'으로 잡아놓고  
articles를 돌면서 title, url, comp를 뽑아옴을 알 수 있다!

### 메일 보내보기!

smtplib 라이브러리를 사용해서 로그인을 하고  
받는 사람 정보, 메일 기본 정보, 내용을 설정해준다.

```py
# 로그인하기
s = smtplib.SMTP_SSL('smtp.gmail.com')
s.login(me, my_password)

# 받는 사람 정보
emails = ['my@email.com']

# ... 생략

# 메일 보내고 서버 끄기
s.sendmail(me, you, msg.as_string())
```

위의 코드처럼 해서 메일이 보내짐을 확인했다!
(''안에 있는 내용은 각자 넣어주어야 한다!)

## 후기

기사 크롤링이 좀 까다로웠는데,  
혼자 계속 고민해보고 해결해볼 수 있어서 좋았다.  

게다가 xlsx 파일로도 변환해보고  
메일로도 보내보는 경험이 흔하진 않은데  
이번 경험으로 한번 해봐서 즐거웠다!