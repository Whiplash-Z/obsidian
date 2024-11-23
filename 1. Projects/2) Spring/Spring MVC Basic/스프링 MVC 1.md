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


# HTTP 메시지 컨버터
>[!Note] 스프링 MVC는 다음의 경우에 HTTP 메시지 컨버터를 적용한다.
>- HTTP 요청: @RequestBody, HttpEntity(RequestEntity)
>- HTTP 응답: @ResponseBody, HttpEntity(ResponseEntity)

## HTTP 요청 데이터 읽기
- HTTP 요청이 오고, 컨트롤러에서 @RequestBody, HttpEntity 파라미터를 사용
- 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 canRead()를 호출
	- 대상 클래스 타입을 지원하는가?
		- ex) @RequestBody의 대상 클래스(byte[], String, HelloData)
	- HTTP 요청의 Content-Type 미디어 타입을 지원하는가?
		- ex) text/plain, application/json, \*/*
	- canRead() 조건을 만족하면 read()를 호출해서 객체를 생성하고 반환
- 컨트롤러에서 @ResponseBody, HttpEntity로 값이 반환된다.
- 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 canWrite()를 호출
	- 대상 클래스 타입을 지원하는가?
		- ex) @ResponseBody의 대상 클래스(byte[], String, HelloData)
	- HTTP 요청의 Accept 미디어 타입을 지원하는가?
		- ex) text/plain, application/json, \*/*
	- canWrite() 조건을 만족하면 write()를 호출해서 HTTP 응답 메시지 바디에 데이터 생성

![[Pasted image 20241123183002.png]]
![[Pasted image 20241123183025.png]]
![[Pasted image 20241123183036.png]]

- @RequestMapping을 처리하는 핸들러 어댑터인 `RequestMappingHandlerAdapter`가 핵심!
- ArgumentResolver
	- 애노테이션 기반의 컨트롤러는 매우 다양한 파라미터를 사용할 수 있다.
		- HttpServletRequest, Model, @RequestParam, @ModelAttribute,
		  @RequestBody, HttpEntity ...
		- 이렇게 파라미터를 유연하게 처리할 수 있는 이유가 바로 `ArgumentResolver` 덕분이다 !
	- RequestMappingHandlerAdapter가 ArgumentResolver를 호출해서 컨트롤러(핸들러)가 필요로하는 다양한 파라미터의 값(객체)을 생성한다.
	- 이렇게 파라미터의 값이 모두 준비되면 컨트롤러를 호출하면서 값을 넘겨준다.
		- 동작 방식
			- ArgumentResolver의 supportsParameter()를 호출해서 해당 파라미터를 지원하는지 체크
			- 지원하면 resolverArgument()를 호출해서 실제 객체를 생성
			- 이렇게 생성된 객체가 컨트롤러 호출시 넘어가는 것이다.


# 전체 흐름 정리
```java
@RestController
@RequestMapping("/api/members")
public class MemberApiController {

    @PostMapping("/add")
    public Member addMember(@RequestBody Member member) {
        // 요청으로 받은 데이터를 로그로 출력
        System.out.println("Name: " + member.getName());
        System.out.println("Age: " + member.getAge());

        // 응답으로 JSON 형식으로 반환
        return member;
    }
}

```

요청 본문
```
{
    "name": "John",
    "age": 25
}
```

- **클라이언트 요청**
    
    - 클라이언트가 POST 요청을 보냅니다.
    - 요청의 **본문(Request Body)**에 JSON 데이터를 포함합니다.
- **DispatcherServlet 동작 시작**
    
    - 요청을 수신하고 처리 흐름을 시작합니다.
- **HandlerMapping**
    
    - `RequestMappingHandlerMapping`이 URL(`/api/members/add`)을 분석하여 적합한 핸들러를 찾습니다.
    - 결과: `MemberApiController.addMember()` 메서드.
- **HandlerAdapter**
    
    - `RequestMappingHandlerAdapter`가 선택되어 메서드 호출을 준비합니다.
- **ArgumentResolver**
    
    - 메서드 매개변수에 있는 **`@RequestBody Member`**를 분석합니다.
- **HttpMessageConverter**
    
    - `@RequestBody`가 요청 본문(JSON)을 **`Member` 객체**로 변환하기 위해 **`HttpMessageConverter`**를 사용합니다.
    - 일반적으로 JSON 변환은 `MappingJackson2HttpMessageConverter`가 담당합니다.
```
{
    "name": "John",
    "age": 25
}
```
-> to convert Java Object
```java
Member member = new Member();
member.setName("John");
member.setAge(25);
```
- **컨트롤러 메서드 실행**
    
    - 변환된 `Member` 객체가 메서드의 매개변수로 전달됩니다.
    - 메서드 실행 결과로 반환된 객체(`Member`)는 응답 본문으로 사용됩니다.
- **HttpMessageConverter (응답 변환)**
    
    - 메서드 반환값이 `@ResponseBody` 또는 `RestController`로 표시되어 있으므로 응답 본문에 데이터를 포함해야 합니다.
    - `Member` 객체를 JSON으로 변환합니다:
```
{
    "name": "John",
    "age": 25
}

```