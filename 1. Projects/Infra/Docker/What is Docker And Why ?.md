# What is Docker ?
> 도커는 컨테이너 기술이다.
Docker is a container technology: A tool for creating and managing containers.

- 소프트웨어 개발에서 컨테이너는 **표준화된 소프트웨어 유닛**이다.
	- 코드 패키지
	- 해당 코드를 실행하는데 필요한 종속성(dependencies to run that code)과 도구가 포함되어 있다.
- 도커는 결국 컨테이너의 생성 및 관리 프로세스를 단순화 하는 도구이다.
	- Support for Containers is built into modern operating systems.
	- Docker simplifies the creation and management of such containers.

# Why Use Docker ?
- 제품 생산에서 가지고 있는 애플리케이션 (version - 14.3)과 똑같은 환경을 유지할 수 있다. 만약 다른 환경에서 버전이 다르다면(version - 11.2) 동일하게 작동하지 않을 수 있다.
	- We want to have the exact same environment for development and production
	- This ensures that it works exactly as tested.
- 코드가 항상 정확한 버전으로 실행되도록 할 수 있다.
