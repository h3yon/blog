---
title: "[Github]올라간 파일/디렉토리 삭제하기"
date: 2021-08-26T23:47:34+09:00
draft: false
categories:
  - github
tags:
  - github
---


### 올라간 파일/디렉토리 삭제하기

.gitignore에 안 올릴 파일을 명시하고 올렸어야 했는데,<br>
까먹고 그냥 올려버렸다.

1. 로컬 디렉토리와 git 저장소에서 모두 삭제

   ```
   $ git rm a.txt
   $ git commit -m "chore: delete a.txt"
   $ git push
   ```

2. git에서만 삭제
   로컬 디렉토리에서는 유지해야 할 때

   ```
   $ git rm --cached a.txt
   $ git commit -m "chore: delete a.txt in git"
   $ git push
   ```
