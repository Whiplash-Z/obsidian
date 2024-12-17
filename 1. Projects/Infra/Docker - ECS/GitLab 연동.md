
# 프로젝트 생성 후 Clone

> SSH Key 생성하기

![[Pasted image 20241204124927.png]]


> 가상머신 - ubuntu

```ssh
➜  Downloads ssh-keygen -t rsa
```
```ssh
➜  Downloads ssh-keygen -t rsa                                                                        
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ubuntu/.ssh/id_rsa
Your public key has been saved in /home/ubuntu/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:N5uCz1c+wDw+svnkqgXx55QWs1e+FriaPDhWxaQmtvE ubuntu@ubuntu
The key's randomart image is:
+---[RSA 3072]----+
|                 |
|            .    |
|      .   o+  .  |
|       o+ o=o+   |
|      ..SOO.o o  |
|       o.*E=.. o |
|      . o+==. o  |
|       +=*=oo.   |
|      .o*B*o .   |
+----[SHA256]-----+
```
> SSH-KEY를 GitLab에 등록하기

방금 생성한 ssh-key는 `/home/ubuntu/.ssh/id_rsa.pub` 경로에 저장이 되어 있다.
![[Pasted image 20241204125617.png]]

이 키를 복사하고 GitLab에 Add SSH key 등록

![[Pasted image 20241204125712.png]]

![[Pasted image 20241204125736.png]]



> Git Clone 가능
```ssh
➜  workspace git clone git@gitlab.com:jackson8314149/docker-sparta.git
Cloning into 'docker-sparta'...
The authenticity of host 'gitlab.com (172.65.251.78)' can't be established.
ED25519 key fingerprint is SHA256:eUXGGm1YGsMAS7vkcx6JOJdOGHPem5gQp4taiCfCLB8.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'gitlab.com' (ED25519) to the list of known hosts.
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (3/3), done.
```

