---
title: "[Github]올라간 파일/디렉토리 삭제하기"
date: 2021-08-26T23:47:34+09:00
draft: false
categories:
  - github
tags:
  - github
---


### 커밋 되돌리기

일단 로그를 먼저 봐야 한다.
아래 명령어를 쳐서 되돌아갈 부분을 보자.

```
% git log --oneline
4170f95 (HEAD -> master, origin/master) Delete replication/slave/data directory
dd16435 Delete replication/master/data directory
b2e987b .gitignore
8a550be .gitignore
4ce5b09 feat: replication
4f40e6f fin: server
```

나는 b2e987b로 돌아가면 된다.
여러가지 옵션이 있는데, 아래 부분을 보고 하나 정해서 돌아가보자.

1. reset

   ```
   $ git reset [돌아갈 부분]
   # ex) git reset b2e987b
   ```

   git reset origin 이후의 커밋들이 전부 사라진다.

2. soft

   ```
   $ git reset --soft [돌아갈 부분]
   # ex) git reset --soft b2e987b
   ```

   log에서 커밋 내용은 사라지나,<br>
   리셋 이후 수정 내용도 동일하게 존재.<br>
   해당 내용이 staging area에 존재.

3. hard

   ```
   $ git reset --hard [돌아갈 부분]
   # ex) git reset --hard b2e987b
   ```

   해당 커밋 이후 내용 모조리 날림<br>
   삭제한 부분을 되돌렸을 때 삭제한 게 바로 나타남을 알 수 있다.<br>
