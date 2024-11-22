# 스프링 MVC 전체 구조
![[Pasted image 20241122231348.png]]
1. 핸들러 조회: 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회
2. 핸들러 어댑터 조회: 핸들러를 실행할 수 있는 핸들러 어댑터를 조회
3. 핸들러 어댑터 실행: 핸들러 어댑터를 실행
4. 핸들러 실행: 핸들러 어댑터가 실제 핸들러를 실행
5. ModelAndView 반환: 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환
6. viewResolver 호출: 뷰 리졸버를 찾고 실행
7. View 반환: 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환
8. 뷰 렌더링: 뷰를 통해서 뷰를 렌더링

# 핸들러 매핑과 핸들러 어댑터
![[Pasted image 20241123000642.png]]
![[Pasted image 20241123000918.png]]


# 스프링 MVC

- @RequestMapping
	- RequestMappingHandlerMapping
	- RequestMappingHandlerAdapter
- @Controller
	- 스프링이 자동으로 스프링 빈으로 등록한다.
	  (내부에 @Component가 있어서 컴포넌트 스캔의 대상이 됨)
- @RequestMapping
	- 요청 정보를 매핑한다.
	- 해당 URL이 호출되면 이 메서드가 호출된다.
- ModelAndView
	- 모델과 뷰 정보를 담아서 반환한다.
- **RequestMappingHandlerMapping**
	- 스프링 빈 중에서`@RequestMapping` 또는 `@Controller`가 클래스 레벨에 붙어 있는 경우 매핑 정보로 인식한다.
- **RequestMappingHandlerAdapter**
	- `@RequestMapping`, `@GetMapping`, `@PostMapping` 등의 어노테이션이 붙은 메서드를 처리하는 어댑터
- 실행 과정 요약
	- **RequestMappingHandlerMapping**: 핸들러(컨트롤러)를 찾아 반환.
	    - 결과값: `SpringMemberControllerV3`.
	- **RequestMappingHandlerAdapter**: 핸들러(컨트롤러)의 메서드 중에서 요청 조건에 맞는 **핸들러 메서드**를 실행.
	    - `/springmvc/v3/members/new-form` → `newForm()` 실행.
		- `/springmvc/v3/members/save` → `save()` 실행.
		- `/springmvc/v3/members` → `members()` 실행.

# 기본 기능
