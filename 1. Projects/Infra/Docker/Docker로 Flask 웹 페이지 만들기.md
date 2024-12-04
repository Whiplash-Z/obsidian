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
