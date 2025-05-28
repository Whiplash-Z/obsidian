![[Pasted image 20250528140959.png]]
- Kernel
	- 하드웨어와 소프트웨어간의 상호작용을 가능하게 해준다.
	- 운영체제의 핵심 Core !!
- Shell
	- 운영체제의 인터페이스가 되는 소프트웨어
	- 사람이 읽을 수 있는 언어를 운영체제(Kernel)에게 번역해주는 역할
- Terminal
	- Shell을 실행하는 Application

```Shell
[사용자]
   ↓
[Terminal] - 사용자가 명령어 입력
   ↓
[Shell] - 명령어 해석 및 변환
   ↓
[Kernel] - 시스템 콜 통해 실제 하드웨어에게 요청
   ↓
[CPU, Memory, Devices] - 실제 연산과 처리
   ↓
[결과를 위로 전달 (역순)]
```
