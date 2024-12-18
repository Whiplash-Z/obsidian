# Dockerfile 스크립트 작성
>[!Info] Dockerfile: 도커 이미지를 빌드하기 위한 명령어와 설정을 정의한 스크립트 파일

```Dockerfile
# 베이스 이미지  
FROM node  
  
# 작업 디렉토리 설정  
# 도커에게 모든 후속 명령이 이 폴더(/app) 내부에서 실행될 것임을 알린다.  
WORKDIR /app  
  
# 도커에게 로컬 머신에 있는 파일이 이미지에 들어가야 하는지 알려야 한다.  
COPY . /app  
  
# 노드 애플리케이션의 모든 종속성을 설치하기 위해 npm install을 실행해야 한다.  
# 도커 컨테이너 및 이미지의 작업 디렉토리에서 실행된다.  
RUN npm install # 이미지를 생성할 때 이 명령어를 실행해야하는 이유가 무엇일까?  
  
# 컨테이너가 시작될 때 우리의 로컬 시스템에 특정 포트를 노출하고 싶다는 것을 도커에게 알린다.  
# 80 포트를 수신하고 있는 컨테이너를 실행할 수 있게 된다.  
EXPOSE 80  
  
# CMD: 이미지가 생성될 때 실행되지 않고, 이미지를 기반으로 컨테이너가 시작될 때 실행된다.  
CMD ["node", "server.js"]
```

> Docker 이미지와 컨테이너의 차이

- 이미지: 컨테이너를 실행하기 위한 "템플릿"
	- 불변성(Immutable)을 가지며, 실행 가능한 애플리케이션과 모든 종속성이 포함되어있다.
	- 이미지를 바탕으로 여러 컨테이너를 생성할 수 있다.
- 컨테이너: 이미지를 기반으로 실행 중인 인스턴스
	- 실제로 작동하며, 특정 작업을 수행하는 실행 환경

>[!Question] 이미지를 생성할 때 RUN npm install을 하는 이유가 무엇일까?
- RUN npm install 명령어는 이미지를 생성하는 과정에서 애플리케이션의 모든 종속성을 설치한다.
- 이 과정을 이미지 생성 시점에 처리하면, 컨테이너를 실행할 때 속도와 효율성을 크게 개선할 수 있다.


>[!Question] 왜 컨테이너 실행 시 npm install을 하지 않을까?
- 속도
	- 이미지 생성 시에 npm install을 실행하면 종속성 설치는 이미지 생성 단계에서만 한 번 수행된다.
	- 이후 동일한 이미지를 기반으로 컨테이너를 생성할 때는 종속성을 다시 설치할 필요가 없다.
	- 결과: 컨테이너 생성 속도가 매우 빨라진다.
- 일관성
	- 종속성을 이미지 생성 단계에서 설치하면, 모든 컨테이너가 동일한 종속성을 사용한다.
	- 만약 컨테이너 실행 시마다 npm install을 실행하면, 종속성 버전 차이로 인해 컨테이너 간 불일치가 발생할 가능성이 생긴다.
- 이미지 크기 최적화
	- RUN npm install은 이미지를 생성할 때 실행되며, 설치된 종속성은 이미지 레이어에 저장된다.
	- 이미지 크기가 커질 수 있지만, 컨테이너 실행 시 추가적인 파일 다운로드가 필요 없으므로 네트워크 부하를 줄일 수 있다.
- 컨테이너 환경 격리
	- 컨테이너 실행 시 npm install을 하면 네트워크 상태에 따라 실패할 가능성이 생긴다.
	- 이미지를 생성할 때 모든 종속성을 설치해 두면 네트워크 문제와 무관하게 컨테이너 실행이 가능하다.


# DockerImage Build

![[Pasted image 20241218154347.png]]

```
> docker images
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
<none>        <none>    42444ad7f89e   15 minutes ago   1.13GB
```

```
> docker run -p 3000:80 42444ad7f89e
```

```
> docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS          PORTS                  NAMES
391dbafaa3d2   42444ad7f89e   "docker-entrypoint.s…"   16 seconds ago   Up 15 seconds   0.0.0.0:3000->80/tcp   pedantic_euler
```


>[!Warning] EXPOSE
- Dockerfile의 `EXPOSE 80`은 선택사항이다.
	- 컨테이너의 프로세스가 이 포트를 노출할 것임을 **문서화 하기 위함이다.**
	- `docker run`을 실행할 때 `-p`를 사용해서 실제 포트를 노출시켜야 한다.
- Dockerfile에 EXPOSE를 추가하여 이 동작을 문서화 하는 것을 권장한다.


