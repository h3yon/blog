---
title: "[Jenkins]AWS EC2 Ubuntu에 Jenkins 연동하기"
date: 2021-09-15T23:47:34+09:00
draft: false
categories:
  - nodejs
tags:
  - nodejs
  - jenkins
  - aws
---


### 1. AWS EC2 Ubuntu에 Jenkins를 연동해보자

#### 관련 패키지 설치

1. Jenkins 설치를 위해 Repository key 추가

   ```
   $ wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
   ```

2. 서버의 sources.list에 Jenkins 패키지 저장소를 추가

   ```
   $ sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
       /etc/apt/sources.list.d/jenkins.list'
   ```

3. 패키지 업데이트 및 Jenkins 설치

   ```
   $ sudo apt-get update
   $ sudo apt-get install jenkins
   ```

4. Java JDK 8 설치
   ```
   $ sudo apt-get install openjdk-8-jdk
   $ java -version
   ```
   `java -version`으로 1.8.0\_\*이 뜨는지 확인한다.

#### Jenkins 실행

아래 명령어로 젠킨스를 실행하고,  
상태를 체크해본다.

```
$ sudo systemctl start jenkins
$ sudo systemctl status jenkins
```

<img src="https://user-images.githubusercontent.com/46602874/133364949-3a21085d-4445-402d-b12f-7e553bbdaad1.png">

위의 이미지처럼 상태가 잘 나타남을 알 수 있다.

#### Jenkins 관련 설정

- Jenkins 포트 변경

    <img src="https://user-images.githubusercontent.com/46602874/133365091-60f1d9d6-14e8-423d-8230-25196710ede1.png">
    ```
    $ sudo vi /etc/default/jenkins
    ```
    jenkins 부분을 vi 편집기로 열어서  
    `HTTP_PORT=[다른 포트 번호]`로 바꿔준다.  
    8080을 많이 사용하다보니 충돌이 일어날 것 같기 때문이다.

  ```
  $ sudo systemctl restart jenkins
  ```

  위의 명령어로 Jenkins를 재실행해준다.

- 방화벽 설정

  ufw를 사용하여 9090 포트로 접근 가능하게 한다.

  ```
  $ sudo ufw allow 9090
  $ sudo ufw status
  ```

  만약 아래처럼 inactive가 나타나면  
   아래처럼 따라해주자

  ```
  $ sudo ufw status
  Status: inactive
  $ sudo ufw enable
  Command may disrupt existing ssh connections. Proceed with operation (y|n)?y
  Firewall is active and enabled on system startup
  $ sudo ufw allow 443 # 이 부분은 선택적으로! 저는 80, 443 추가해주었습니다
  $ sudo ufw status
  ```

  그럼 status에서 80, 443, 9090 등 잘 추가된 부분을 확인할 수 있다.

  - 추가
    <img src="https://user-images.githubusercontent.com/46602874/133367016-ee87620a-24dc-47ff-8032-7124b7c6af70.png">

  혹시 접속하려는 젠킨스 포트가 인바운드 규칙에  
  추가되어 있지 않다면 꼭 추가해주자

#### Jenkins 접속

`http://(public IP or hostname):9090`으로 접속하면  
아래와 같은 화면이 나온다.

<img src="https://user-images.githubusercontent.com/46602874/133367143-63d31f03-0c91-4a21-9fab-5db6efc2eb36.png">

비밀번호를 알아야되기 때문에 사진에 나온 것처럼  
아래의 명령어로 비밀번호를 알아내고 넣어주자.

```
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
aaabdc~~
```

그럼 다음 화면으로  
플러그인 설치 화면이 나온다.  
대충 왼쪽 부분을 눌러주자.

<img src="https://user-images.githubusercontent.com/46602874/133367376-76cc42a3-8146-474b-bb9c-86fc453ad2b9.png">

Create First Admin User이 나오고,  
앞으로 로그인해야되기 때문에  
많이 사용하는 계정 정보를 넣어주자

<img src="https://user-images.githubusercontent.com/46602874/133370174-7c42b0fc-04e3-4f36-a672-7f72e56cd804.png">

그럼 이렇게 잘 접속됨을 확인할 수 있다!

<img src="https://user-images.githubusercontent.com/46602874/133370432-073fec58-49fe-4cfe-bca5-3175395aa95e.png">

출처: https://imbf.github.io/devops/2020/11/26/Install-Jenkins-in-Ubuntu(18.04).html

(추가)nodejs, jenkins ec2 환경 구축 시 참고하면 좋은 부분: https://velog.io/@dlawogus/AWS-ec2-%EC%84%9C%EB%B2%84-%EB%93%B1%EB%A1%9D
