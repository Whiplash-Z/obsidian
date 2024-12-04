![[Pasted image 20241204134118.png]]

>Dockerfile 작성

```ssh
FROM python:3.9-slim
WORKDIR /opt/app
COPY . /opt/app
RUN pip install -r requirements.txt
CMD ["python3", "app.py"]
```

>Dockerfile build -> Docker Image 생성

```ssh
docker build -t Docker-sparta/test-app:0.1 .
```

>Docker Image 확인

```ssh
➜  test-app git:(main) ✗ docker images
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
Docker-sparta/test-app   0.1       df2281f60dd4   7 minutes ago   180MB
```

>Docker Run

```ssh
➜  test-app git:(main) ✗ docker run --name test-app -p 5000:5000 Docker-sparta/test-app:0.1 
```
![[Pasted image 20241204134417.png]]

```ssh
➜  test-app git:(main) ✗ curl localhost:5000
Hello, Flask World!       
```

# Docker Image 저장소 (ECR)
![[Pasted image 20241204142841.png]]

>[!Question] private repository만 생성이 가능한가? public은 불가능?

# ECR push를 위한 IAM 권한 설정
![[Pasted image 20241204143427.png]]
![[Pasted image 20241204143445.png]]
- 기존에 Admin 권한은 추가해둔 상태이다.
- 만약 처음으로 유저를 추가한다면 해당 권한을 추가해줘야 한다.


> Create access Key

![[Pasted image 20241204143542.png]]
![[Pasted image 20241204143610.png]]
- -> 액세스 키 만들기하면 끝.

> .csv file 다운로드

![[Pasted image 20241204143658.png]]


# AWS CLI 설치
[AWS CLI 설치 공식문서](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/getting-started-install.html#:~:text=Command%20line%20%2D-,Linux,-ARM)
![[Pasted image 20241204144010.png]]

```ssh
➜  workspace cat Administrator_accessKeys.csv 
Access key ID,Secret access key
AKIAWPP**736PCH4A**,YbB224cnUQAIQHB1kbr5k**JoEWVlYp522X+bKH**
```

```ssh
➜  workspace aws configure
AWS Access Key ID [None]: AKIAWPP**736PCH4A**
AWS Secret Access Key [None]: YbB224cnUQAIQHB1kbr5k**JoEWVlYp522X+bKH**
Default region name [None]: ap-northeast-2
Default output format [None]: JSON
```

> 설정 검증

```ssh
➜  workspace cd                              
➜  ~ cd .aws/    
➜  .aws ls
config  credentials
➜  .aws cat config                      
[default]
region = ap-northeast-2
output = JSON
➜  .aws cat credentials 
[default]
aws_access_key_id = AKIAWPP**736PCH4A**
aws_secret_access_key = YbB224cnUQAIQHB1kbr5k**JoEWVlYp522X+bKH**
```

# ECR에 Docker Image PUSH
![[Pasted image 20241204145313.png]]
![[Pasted image 20241204145331.png]]

```ssh
➜  ~ aws ecr get-login-password --region ap-northeast-2 | docker login --username AWS --password-stdin 445567074044.dkr.ecr.ap-northeast-2.amazonaws.com
WARNING! Your password will be stored unencrypted in /home/ubuntu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credential-stores

Login Succeeded
```

![[Pasted image 20241204145911.png]]

> PUSH 확인

![[Pasted image 20241204150001.png]]


> ECR에 Push한 이미지로 서버 실행해보기
![[Pasted image 20241204150456.png]]

```ssh
➜  ~ docker run --name ecr-test -p 5000:5000 445567074044.dkr.ecr.ap-northeast-2.amazonaws.com/docker/test-app:0.1
Unable to find image '445567074044.dkr.ecr.ap-northeast-2.amazonaws.com/docker/test-app:0.1' locally
0.1: Pulling from docker/test-app
1dd4a0e7ae4e: Already exists 
e88090b39a19: Already exists 
3d85cafe0389: Already exists 
b45d7e67b243: Already exists 
4e278b781ab0: Already exists 
7578e51e9f7b: Already exists 
4f822f7547aa: Already exists 
Digest: sha256:101cc2f0b4aa0a4cba91657dd80d77f87cc52c077d8f7df640b6719950a18013
Status: Downloaded newer image for 445567074044.dkr.ecr.ap-northeast-2.amazonaws.com/docker/test-app:0.1
 * Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000
Press CTRL+C to quit
172.17.0.1 - - [04/Dec/2024 06:02:49] "GET / HTTP/1.1" 200 -
```

```ssh
➜  ~ curl localhost:5000                                                               
Hello, Flask World!%     
```

# GitLab을 이용한 Push 자동화
![[Pasted image 20241204150935.png]]


> GitLab Runner 설치

[GitLab Runner Install 공식문서](https://docs.gitlab.com/runner/install/linux-manually.html)

현재 나의 가상머신은 arm64를 사용하고 있기 때문에 arm64로 값을 치환한다.
```ssh
curl -LJO "https://s3.dualstack.us-east-1.amazonaws.com/gitlab-runner-downloads/latest/deb/gitlab-runner_arm64.deb"
```

>dpkg -i 명령어로 설치 시작
```ssh
sudo dpkg -i gitlab-runner_arm64.deb 
```

![[Pasted image 20241204152238.png]]

# GitLab Runner 실행
![[Pasted image 20241204152834.png]]
![[Pasted image 20241204152945.png]]

![[Pasted image 20241204153316.png]]

# 시스템 부팅 시 자동으로 GitLab Runner 실행하도록 설정

```ssh
➜  .gitlab-runner sudo su -
root@ubuntu:~# cd /etc/systemd/system/
root@ubuntu:/etc/systemd/system# vi gitlab-runner.service 
```

![[Pasted image 20241204160122.png]]


```ssh
➜  ~ ls -al .bash_logout
-rw-r--r-- 1 ubuntu ubuntu 220 Mar 31  2024 .bash_logout
```
![[Pasted image 20241204160324.png]]

> 변경된 설정 바로 적용하기

```ssh
➜  ~ sudo systemctl restart gitlab-runner
Warning: The unit file, source configuration file or drop-ins of gitlab-runner.service changed on disk. Run 'systemctl daemon-reload' to reload units.
```

변경을 위해서는 systemctl daemon-reload 명령어를 실행하라고 하기 때문에 아래와 같이 명령어를 실행한다.

```ssh
➜  ~ sudo systemctl daemon-reload                               
➜  ~ sudo systemctl restart gitlab-runner
➜  ~ sudo systemctl enable gitlab-runner
```

enable을 설정하게 되면, 시스템을 시작할 때 자동으로 실행되게 해준다.