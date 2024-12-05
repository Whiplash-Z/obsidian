# 클러스터 생성하기
![[Pasted image 20241205104148.png]]
![[Pasted image 20241205104249.png]]

# Task Deifinitions 생성

> 컨테이너를 실행하기 위해서는 Task Definitions가 필요하다.

![[Pasted image 20241205104514.png]]

![[Pasted image 20241205104633.png]]
- Task role은 IAM에서 직접 역할(role)을 등록해줬다.

## IAM 콘솔에서 Role 설정하는 방법
![[Pasted image 20241205111456.png]]

![[Pasted image 20241205111536.png]]

![[Pasted image 20241205111549.png]]
1. AWS Service 선택
2. service or use case에서 "elastic Container Service" 선택

![[Pasted image 20241205111626.png]]

![[Pasted image 20241205111646.png]]
![[Pasted image 20241205111706.png]]

![[Pasted image 20241205111721.png]]





## Container Image URI

![[Pasted image 20241205104744.png]]

![[Pasted image 20241205104852.png]]
- CREATE

1. 클러스터 생성
2. Task Definitions
3. Cluster에 Service 생성
4. 3에서 만든 service가 컨테이너 생성
5. 4에서 실행될 컨테이너는 2에서 만든 Task Deifinition을 사용해서 실행


# Cluster - Service 생성
![[Pasted image 20241205105137.png]]
![[Pasted image 20241205105148.png]]

![[Pasted image 20241205105302.png]]

![[Pasted image 20241205105503.png]]


![[Pasted image 20241205105805.png]]


![[Pasted image 20241205110033.png]]

- Listener는 사용자가 접근할 ALB의 포트번호
- Target Group은 클라이언트가 웹브라우저로 80번 포트로 접근했을 때
  이 유저를 어디로 보낼지 설정하는 것.
	- 80번 포트로 들어온 유저는 sprata-app-tg로 보낸다.
	- 이 targetGroup에는 인스턴스 또는 컨테이너가 생성된다.


<< Local -> ECR >>
1. 로컬(가상머신(ubuntu))에서 코드를 작성하고 GltLab에 Push

2. GitLab Runner가 main에 코드가 업데이트가 된 것을 확인하고 .gitlab-ci.yml에 작성된 스크립트 대로 명령어를 수행

3. Dockerfile을 빌드하고 생성된 이미지를 ECR로 push 하도록 작성되어 있으므로 ECR에 이미지 저장

<< Cluster -> service >>
4. 클러스터에서 생성된 service는 TaskDefinition에서 설정한 ECR URI를 리스닝하고, 업데이트가 되면 해당 이미지로 컨테이너를 생성한다.

5. 2개의 Task로 정의했기 때문에 2개의 Docker Container 생성

6. service에서 로드밸런서를 생성할 때 80포트를 Listening하고, 이 요청을 target group으로 전달

7. 클라이언트로부터 80포트로 요청이 들어오면 로드밸런서가 요청을 받고 타겟그룹으로 전달.

8. 이 타겟그룹은 생성된 service(container)를 의미.
