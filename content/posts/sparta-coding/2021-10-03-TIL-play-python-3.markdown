---
title: "[TIL] 힙한취미코딩! 파이썬 혼자놀기 패키지 3주차 개발일지"

toc: true
toc_sticky: true

categories:
  - Crawling
tags:
  - Crawling
---

## [TIL] 힙한취미코딩! 파이썬 혼자놀기 패키지 3주차 개발일지

스파르타코딩클럽에서  
파이썬 혼자놀기 패키지가 있어서 3주차를 들어보았다! 

## 오늘의 목표

word 클라우드를  
아래처럼 만들어보기다!  

<img src="https://user-images.githubusercontent.com/46602874/135745005-99546f43-d069-47cf-a406-368fafaf9f0c.png">

정리 코드: [코드](https://github.com/h3yon/Crawling-TEST/commit/1a99597756722fab7a4597e952f69d917419ac3f)

## 사용 모듈 살펴보기

wordcloud, random, Image 등의  
라이브러리를 사용함을 알 수 있다.

```py
from wordcloud import WordCloud
from PIL import Image
import numpy as np
import random
```

## 방법

일단 아무거나 카카오톡에서 csv를 추출한다.  
나는 이모지가 많고 예쁜 스파르타코딩클럽과의 톡으로 해봤다!

<img src="https://user-images.githubusercontent.com/46602874/135745117-5dfc4d1e-0d9f-4496-a325-7966cfdf1ffe.png">

```py
with open("kakao.csv", "r", encoding="utf-8") as f:
    lines = f.readlines()
    for line in lines:
        result = line.split(',"')
        if len(result) == 3:
            text += result[2].replace('ㅋ','').replace('ㅠ','').replace('채널추가하고','').replace('ㅜ','').replace('이모티콘','').replace('사진','').replace('"','')
```

저장한 csv파일을 불러오고,  
line을 하나하나 읽으면서  
replace로 제외하고 싶은 부분은 제외했다

```py
def grey_color_func(word, font_size, position, orientation, random_state=None,
                    **kwargs):
    return "hsl(0, 0%%, %d%%)" % random.randint(60, 100)

# mask = np.array(Image.open('cloud.png'))
mask = np.array(Image.open('sherlock.png'))
wc = WordCloud(font_path='/Users/we/Library/Fonts/SCDream5.otf', background_color="Black", mask=mask, max_font_size=100)
wc.generate(text)
wc.recolor(color_func=grey_color_func)
wc.to_file("sherlock_masked.png")
```

원하는 색상을 넣고,  
셜록 png를 넣었다!  

## 후기

이번 파이썬 혼자놀기로 정말 재밌게 혼자 놀았다!  

<img src="https://user-images.githubusercontent.com/46602874/135745117-5dfc4d1e-0d9f-4496-a325-7966cfdf1ffe.png">

추석연휴 때 뭐할까 고민했었는데,  
이렇게 재밌게 보내서 너무 즐거웠다!  

워드 클라우드도 만들고,  
크롤링도 해보고 바쁜 일상을 보내기 전에  
자기계발도 하고 힐링도 했다!  
최고최고~!