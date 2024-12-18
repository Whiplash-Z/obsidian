![[Pasted image 20241217202422.png]]

- 예외가 발생해서 서블릿을 넘어 WAS까지 예외가 전달되면 HTTP 상태코드가 500으로 처리된다.
	- 발생하는 예외에 따라서 400, 404 등등 다른 상태로 처리하고 싶다.
	- 오류 메시지, 형식 등을 API 마다 다르게 처리하고 싶다.

# HandlerExceptionResolver
- 스프링 MVC는 컨트롤러(핸들러) 밖으로 예외가 던져진 경우 예외를 해결하고, 동작을 새로 정의할 수 있는 방법을 제공한다.
- 컨트롤러 밖으로 던져진 예외를 해결하고, 동작 방식을 변경하고 싶으면 HandlerExceptionResolver를 사용하면 된다.
- 줄여서 ExceptionResolver라 한다.

>[!Info] ExceptionResolver로 예외를 해결해도 postHandle()은 호출되지 않는다.


> 정리

- ExceptionResolver를 사용하면 컨트롤러에서 예외가 발생해도 ExceptionResolver에서 예외를 처리한다.
- 따라서 예외가 발생해도 서블릿 컨테이너까지 예외가 전달되지 않고, 스프링 MVC에서 예외 처리는 끝난다.
- **결과적으로 WAS 입장에서는 정상 처리가 된 것이다.**
- 예외를 이곳에서 모두 처리할 수 있다는 것이 핵심이다.


# 스프링이 제공하는 ExceptionResolver
- 직접 ExceptionResolver를 구현하려고 하니 상당히 복잡하다.
- 스프링이 제공하는 ExceptionResolver를 사용하자.
> handlerExceptionResolverComposite에 다음 순서로 등록한다.

![[Pasted image 20241217203315.png]]
- ExceptionHandlerExceptionResolver
	- @ExceptionHandler를 처리한다.
	- API 예외 처리는 대부분 이 기능으로 해결한다.
- ResponseStatusExceptionResolver
	- HTTP 상태 코드를 지정해준다.
	- `@ResponseStatus(value = HttpStatus.NOT_FOUND)`
- DefaultHandlerExceptionResolver
	- 스프링 내부 기본 예외를 처리한다.


## ResponseStatusExceptionResolver
- 다음 두 가지 경우를 처리한다.
	- @ResponseStatus가 달려있는 예외
	- ResponseStatusException 예외
```java
import org.springframework.http.HttpStatus; import org.springframework.web.bind.annotation.ResponseStatus;

@ResponseStatus(code = HttpStatus.BAD_REQUEST, reason = "잘못된 요청 오류") public class BadRequestException extends RuntimeException { }
```
- BadRequestException 예외가 컨트롤러 밖으로 넘어가면 ResponseStatusExceptionResolver 예외가 해당 애노테이션을 확인해서 오류 코드를 HttpStatus.BAD_REQUEST(400)으로 변경하고, 메시지도 담는다.

## DefaultHandlerExceptionResolver
- 스프링 내부에서 발생하는 스프링 예외를 해결한다.
- 대표적으로 파라미터 바인딩 시점에 타입이 맞지 않으면 내부에서 TypeMissmatchException이 발생하는데, 이 경우 예외가 발생했기 때문에 그냥 두면 서블릿 컨테이너까지 오류가 올라가고, 결과적으로 500 오류가 발생한다.
	- 파라미터 바인딩은 대부분 클라이언트가 HTTP 요청 정보를 잘못 호출해서 발생하는 문제이기 때문에 이 경우 HTTP 상태코드 400을 사용하도록 되어 있다.
	- DefaultHandlerExcpetionResolver는 이것을 500 오류가 아니라 500 오류로 변경한다.

## @ExceptionHandler
- 웹 브라우저에 HTML 화면을 제공할 때는 오류가 발생하면 BasicErrorController를 사용하는게 편하다.
	- 이때는 단순히 5xx, 4xx 관련된 오류 화면을 보여주면 된다.
	- BasicErrorController는 이런 메커니즘을 모두 구현해두었다.
- 그런데 API는 각 시스템 마다 응답의 모양도 다르고, 스펙도 모두 다르다.
	- 예외 상황에 단순히 오류 화면을 보여주는 것이 아니라, 예외에 따라서 각각 다른 데이터를 출력해야 할 수도 있다.
	- 그리고 같은 예외라고 해도 어떤 컨트롤러에서 발생했는가에 따라서 다른 예외 응답을 내려주어야 할 수 있다.
	- 한마디로 매우 세밀한 제어가 필요하다
- API 예외처리의 어려운 점
	- HandlerExceptoinResolver를 떠올려보면, ModelAndView를 반환해야 했다.
		- 이것은 API 응답에는 필요하지 않다.
	- API 응답을 위해서 HttpServletResponse에 직접 응답 데이터를 넣어주었다.
		- 이것은 매우 불편하다.
		- 스프링 컨트롤러에 비유하면, 마치 과거 서블릿을 사용하던 시절로 돌아간 것 같다.
	- 특정 컨트롤러에서만 발생하는 예외를 별도로 처리하기 어렵다.
		- 예를 들어서, 회원을 처리하는 컨트롤러에서 발생하는 RuntimeException 예외와 상품을 관리하는 컨트롤러에서 동일한 RuntimeException 예외를 서로 다른 방식으로 처리하고 싶다면 어떻게 해야할까?

>[!Success] @ExceptionHandler

스프링은 API 예외 처리 문제를 해결하기 위해 @ExceptionHandler이라는 애노테이션을 사용하는 매우 편리한 예외처리 기능을 제공하는데, 이것이 바로 `ExceptionHandlerExceptionResolver`이다.

```java
@Slf4j  
@RestControllerAdvice(basePackages = "hello.exception.api")  
public class ExControllerAdvice {  
  
    @ResponseStatus(HttpStatus.BAD_REQUEST)  
    @ExceptionHandler(IllegalArgumentException.class)  
    public ErrorResult illegalExHandler(IllegalArgumentException e) {  
        log.error("[exceptionHandler] ex", e);  
        return new ErrorResult("BAD", e.getMessage());  
    }  
  
    @ExceptionHandler  
    public ResponseEntity<ErrorResult> userExHandler(UserException e) {  
        log.error("[exceptionHandler] ex", e);  
        ErrorResult errorResult = new ErrorResult("USER-EX", e.getMessage());  
        return new ResponseEntity(errorResult, HttpStatus.BAD_REQUEST);  
    }  
  
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)  
    @ExceptionHandler  
    public ErrorResult exHandler(Exception e) {  
        log.error("[exceptionHandler] ex", e);  
        return new ErrorResult("EX", "내부 오류");  
    }  
  
}
```

> @ExceptionResolver 예외 처리 방법

- @ExceptionResolver 애노테이션을 선언하고, 해당 컨트롤러에서 처리하고 싶은 예외를 지정해주면 된다.
- 지정한 예외 또는 그 예외의 자식 클래스는 모두 잡을 수 있다.

> 다양한 예외
```java
@ExceptionHandler({AException.class, BException.class}) 
public String ex(Exception e) { log.info("exception e", e); }
```
- 다음과 같이 다양한 예외를 한번에 처리할 수 있다.

> 예외 생략

```java
@ExceptionHandler 
public ResponseEntity<ErrorResult> userExHandle(UserException e) {}
```
- 생략하면 메서드 파라미터의 예외가 지정된다.

> 실행 흐름
- 컨트롤러 호출 -> IllegalArgumentException 예외가 컨트롤러 밖으로 던져진 경우
- 예외가 발생했으므로 ExceptionResolver가 작동한다.
	- 가장 우선 순위가 높은 ExceptionHandlerExceptionResolver가 실행된다.
- ExceptionHandlerExceptionResolver는 해당 컨트롤러에 IllegalArgumentException을 처리할 수 있는 @ExceptionHandler가 있는지 확인한다.
- illegalExHandler() 메서드에 선언이 되어 있으므로 이 메서드를 실행한다.
- @RestController가 있는 컨트롤러에는 @ResponseBody가 적용이 되고, HTTP 컨버터가 사용되어 응답에는 JSON으로 반환된다.
- @ResponseStatus(HttpStatus.BAD_REQUEST)를 지정했으므로 HTTP 상태 코드 400으로 응답한다.


```
{
"code": "BAD", 
"message": "잘못된 입력 값"
}
```


# @ControllerAdvice
@ControllerAdvice 또는 @RestControllerAdvice를 사용하면 정상 코드와 예외 처리 코드의 로직을 분리할 수 있다.

![[Pasted image 20241217210544.png]]

- 스프링 공식 문서 예제에서 보는 것 처럼 특정 애노테이션이 있는 컨트롤러를 지정할 수 있고, 특정 패키지를 직접 지정 할 수도 있다.
- 패키지 지정의 경우 해당 패키지와 그 하위에 있는 컨트롤러가 대상이 된다. 그리고 특정 클래스를 지정할 수도 있다.
- 대상 컨트롤러 지정을 생략하면 모든 컨트롤러에 적용된다.