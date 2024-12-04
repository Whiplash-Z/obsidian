![[Pasted image 20241204112701.png]]

![[Pasted image 20241204112754.png]]


![[Pasted image 20241204112815.png]]


![[Pasted image 20241204113247.png]]



# EC2에 SSH 연결하기

>644 권한 -> 700 권한으로 수정
```ssh
➜  Downloads ls 
code_1.95.3-1731512059_arm64.deb  ec2-server-key.pem  python
➜  Downloads ls -al ec2-server-key.pem                             
-rw-r--r-- 1 ubuntu ubuntu 1674 Dec  4 02:40 ec2-server-key.pem
➜  Downloads chmod 400 ec2-server-key.pem
➜  Downloads ls -al ec2-server-key.pem   
-r-------- 1 ubuntu ubuntu 1674 Dec  4 02:40 ec2-server-key.pem
➜  Downloads 
```

> 연결

![[Pasted image 20241204114405.png]]
```ssh
ssh -i "ec2-server-key.pem" ubuntu@ec2-13-125-250-66.ap-northeast-2.compute.amazonaws.com
```

> 성공

![[Pasted image 20241204114455.png]]



# Docker install
[도커 공식문서 - 설치 (Ubuntu - apt)](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)


# 도커 그룹에 유저를 포함하기
> docker 그룹 추가
```ssh
sudo groupadd docker
```
```
ubuntu@ip-172-31-44-205:~$ sudo groupadd docker
groupadd: group 'docker' already exists
```

> docker 그룹에 현재 유저 추가
```ssh
sudo usermod -aG docker ubuntu
```
```
ubuntu@ip-172-31-44-205:~$ sudo usermod -aG docker ubuntu
ubuntu@ip-172-31-44-205:~$ 
```
> 변경된 내용 적용
```ssh
newgrp docker
```
```
ubuntu@ip-172-31-44-205:~$ newgrp docker
ubuntu@ip-172-31-44-205:~$ 
```


> 이후로는 사용자 권한 인증을 받지 않아도 실행 가능

```ssh
ubuntu@ip-172-31-44-205:~$ docker run --name some-nginx -p 8080:80 -d nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
bc0965b23a04: Pull complete 
650ee30bbe5e: Pull complete 
8cc1569e58f5: Pull complete 
362f35df001b: Pull complete 
13e320bf29cd: Pull complete 
7b50399908e1: Pull complete 
57b64962dd94: Pull complete 
Digest: sha256:fb197595ebe76b9c0c14ab68159fd3c08bd067ec62300583543f0ebda353b5be
Status: Downloaded newer image for nginx:latest
68d89278fe0216264480b8b325e0f960725c65727a87ea6874615b1a58c19d8c
```
