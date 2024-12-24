>[!Info] Volume이란?
>- **호스트 머신의 폴더**
>- 컨테이너나 이미지에 있는 것이 아닌, **호스트 컴퓨터**에 장착된 하드 드라이버에 존재
>- **도커 컨테이너 내부의 폴더에 맵핑**된다.
>- 컨테이너가 종료된 경우에도 데이터가 지속되며 계속 존재한다.
>- 컨테이너 외부에서 엑세스 하려는 폴더와 단순히 컨테이너 종료와 제거 후에도 생존해야 하는 데이터에 사용할 수 있다.


# Anonymus Volume
: 컨테이너가 존재하는 동안에만 데이터 존재
```Dockerfile
FROM node:14  
  
WORKDIR /app  
  
COPY package.json .  
  
RUN npm install  
  
COPY . .  
  
EXPOSE 80  
  
# 컨테이너의 외부 폴더에  매핑되어질 컨테이너 내부 위치
#Anonymus Volume: 도커파일 내부에 작성 가능
VOLUME ["/app/feedback"]  
  
CMD ["node", "server.js"]
```


# Named Volume
: 컨테이너가 종료된 후에도 데이터가 존재
: 도커파일 내부에 작성 불가능 -> 컨테이너를 실행할 때 명령어로 작성

```
> docker run --name feedback-app -p 3000:80 -d --rm -v feedback:/app/feedback feedback-node:volumes
```

![[Pasted image 20241224154622.png]]