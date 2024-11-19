# URI(Uniform Resource Identifier)
![[Pasted image 20241119222401.png]]
- URI: 리소스를 식별하는 통합된 방법
	- 리소스를 식별한다
		- 자원이 어디있는지, 주민등록번호처럼 "식별한다"와 같은 의미
	- URL: Resource Locator = 리소스의 위치 (강준호의 위치)
	- URN: Resource Name = 리소스의 이름 (강준호)
![[Pasted image 20241119222650.png]]
- **U**niform: 리소스를 식별하는 통일된 방식
- **R**esource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)
- **I**dentifier: 다른 항목과 구분하는데 필요한 정보 (주민번호 같은 것)

- URL - Locator: 리소스가 있는 위치를 지정 (검색 -> 리소스가 여기 있을거야!)
- URN - Name: 리소스에 이름을 부여

>https://www.google.com/search?q=hello&hl=ko
- 프로토콜: https
	- 어떤 방식으로 자원에 접근할 것인가?
	- **클라이언트와 서버간의 약속과 규칙**
	- ex) http, https, ftp 등등
- 호스트명: www.google.com 
	- 도메인명 또는 IP 주소를 직접 사용가능
- 포트번호: 443 (생략 가능)
- 패스: /search
	- 리소스 경로, **계층적 구조**
	- ex) /members, /members/100, /items/iphone12
- 쿼리 파라미터: q=hello&hl=ko
	- key=value 형태
	- ?로 시작, &로 추가 가능
	- ex) ?keyA=valueA&keyB=valueB

# 웹브라우저 요청 흐름
1. 검색: https://www.google.com/search?q=hello&hl=ko
	- https://200.200.200.2:443/search?q=hello&hl=ko
		- DNS 조회 -> IP 확인
		- PORT 확인
2. 애플리케이션 계층
	- 웹 브라우저가 HTTP 요청 메시지 생성
	- SOCKET 라이브러리를 통해 OS에 전달
		- TCP/IP 연결(IP, PORT): 3-way hand shake
		- 데이터를 OS(네트워크 계층)에 전달
3. OS(네트워크) 계층
	- HTTP 메시지가 포함된 TCP/IP 패킷 생성
4. 네트워크 인터페이스 계층
	- 목적지 서버에 전달

![[Pasted image 20241119231622.png]]
![[Pasted image 20241119231530.png]]
![[Pasted image 20241119231544.png]]
![[Pasted image 20241119231555.png]]
![[Pasted image 20241119231608.png]]
![[Pasted image 20241119231658.png]]
![[Pasted image 20241119231708.png]]
![[Pasted image 20241119231716.png]]
![[Pasted image 20241119231725.png]]






