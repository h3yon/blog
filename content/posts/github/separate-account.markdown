---
title: "[Github]git 계정 분리해서 사용하기"
date: 2022-05-08T20:34:34+09:00
draft: false
categories:
  - github
tags:
  - github
---

참고 및 출처: [폴더별 github 계정 분리하기](https://velog.io/@bambi-bam/%ED%8F%B4%EB%8D%94%EB%B3%84-github-%EA%B3%84%EC%A0%95-%EB%B6%84%EB%A6%AC%ED%95%98%EA%B8%B0)

한 기기 내에서 2개의 git 계정을 사용하고 싶을 때 git계정을 분리하여 사용할 수 있다.  
위 블로그에 너무 잘 작성되어 있어서 정리해보려고 한다.
회사, 개인용 git 계정을 사용한다고 가정하고 아래처럼 따라할 수 있다!

### 1. SSH Key 생성

window는 ~가 아니라 `c:/{사용자}/{사용자 아이디}` 디렉토리

```
$ mkdir ~/.ssh
$ cd ~/.ssh

$ ssh-keygen -t rsa -C "회사용 github 아이디@email.com" -f "id_rsa_comp"
$ ssh-keygen -t rsa -C "개인용 github 아이디@email.com" -f "id_rsa_user"
```

ssh-keygen 다음 나타나는건 그냥 `모두 엔터` 친다.

### 2. github에 ssh key 공개키(pub) 등록

아래처럼 `new SSH Key`를 클릭한다.

![image](https://user-images.githubusercontent.com/46602874/167294747-4109ee0c-25e4-46e4-b797-ea8557e590dd.png)

아래의 명령어로 공개키를 복사해놓는다.

```
# window는 clip < ~/.ssh/id_user.pub
$ pbcopy < ~/.ssh/id_user.pub
```

아까 new SSH Key를 선택한 화면에다가  
Key 안에 내용을 넣는다.

![image](https://user-images.githubusercontent.com/46602874/167294900-39bc5540-5d8b-4842-94b0-8f63159e4b75.png)

> 회사 계정 또는 다른 개인 계정도 2번 과정을 반복해준다. (id_rsa_comp 또는 설정한 계정)

### 3. config 파일 설정

config 파일을 생성한다.

```
$ vi ~/.ssh/config
```

내용은 아래처럼 작성한다.  
`a` 키를 눌러 insert모드로 변경 후 아래처럼 작성한다.

```
Host github.com-comp
        HostName {회사 도메인 등등}.com
    IdentityFile ~/.ssh/id_rsa_comp
    User {user명}

Host github.com-user
        HostName github.com
    IdentityFile ~/.ssh/id_rsa_user
    User {user명}
```

다 작성했으면 `esc` 키를 누른 다음 `:wq` + `enter`를 눌러 저장해주자.

### 디렉토리별 path 설정

전역 git 설정을 보자.

```
vi ~/.gitconfig
```

아무것도 안 나타나면 그냥 진행하고,  
`[user]` 안에 email, name 이 있다면 통째로 지워주거나 아래처럼 수정하자.  
(마찬가지로 `a` 키로 insert 모드로 변경)

```
[user]
  email = email@adress #user와 comp 중, 잘 사용하는 계정을 놓자
  name = nickname
[includeIf "gitdir:~/corp-repo-dir"]
  path = .gitconfig-corp
```

보통 모두 회사 아이디를 사용할거고,  
user 디렉토리에만 user 깃 계정을 사용할 예정이면 아래처럼 작성 가능하다.

```
[user]
  email = {아이디}@{회사 도메인}.com
  name = {실명 또는 아이디}
[includeIf "gitdir:~/Documents/user"]
  path = .gitconfig-user
```

다 작성했으면 `esc` 키를 누른 다음 `:wq` + `enter`를 눌러 저장해주자.  
위 `path` 에는 `gitconfig-user` 또는 `gitconfig-comp`를 지정해주었을 수도 있고,  
다른 이름을 사용했을 수도 있다.

그 파일을 만들어준다.

```
# 위 path에 지정한 파일 생성 
$ vi ~/.gitconfig-user
```
내용은 회사용 아이디나 개인용 아이디 아무거나 사용할 것으로 넣는다.

```
[user]
        name = {개인용 아이디}
        email = {개인용 아이디}@gmail.com
```
