
1. @Compoent와 @ComponentScan의 동작
	1. @Compoent
		- 클래스에 @Component를 붙이면, 해당 클래스가 스프링 빈으로 등록될 수 있음을 나타낸다.
		- 스프링 컨테이너는 이 클래스를 관리 대상으로 인식한다.
	2. @ComponentScan
		- 특정 패키지와 그 하위 패키지를 스캔하여 @Component, @Controller, @Service, @Repository 등과 같은 애너테이션이 붙은 클래스를 찾아 스프링 빈으로 등록
	3. 빈 생성
		- 스프링 컨테이너는 @ComponentScan을 통해 스캔된 클래스를 대상으로 객체(빈)를 생성하고 의존성을 주입한다.
		- 이 과정에서 new 키워드를 사용하지 않아도, 스프링이 내부적으로 **리플렉션(Reflection)을 사용하여 객체를 생성**한다.
2. 생성자 주입과 스프링 빈의 초기화
	1. 생성자 주입은 객체를 생성할 때 딱 한 번 호출된다.
	2. 스프링이 빈을 생성하는 과정에서 생성자를 호출하여 의존성을 주입한다.
3. 스프링 컨테이너의 빈 초기화 과정
	1. @ComponentScan 탐색
		1. 스프링이 @ComponentScan으로 @Component가 붙은 클래스를 스캔한다.
	2. 빈 정의 등록
		1. 스프링 컨테이너에 해당 클래스의 빈 정의를 등록한다.
		2. 이 단계에서는 실제 객체를 생성하지 않는다.
	3. 빈 생성
		1. 컨테이너가 초기화될 때, 의존성 그래프를 분석하여 필요한 객체들을 생성
		2. 생성자 주입 방식이라면, 스프링이 생성자를 호출하면서 필요한 의존성을 자동으로 주입
		3. 이미 생성된 빈이 있다면, 해당 객체를 재사용

```java
@Component
public class ServiceA {
    private final ServiceB serviceB;

    @Autowired
    public ServiceA(ServiceB serviceB) {
        this.serviceB = serviceB;
    }
}

@Component
public class ServiceB {
    // ServiceB의 로직
}

```
1. 스프링이 애플리케이션 시작 시 `ServiceA`와 `ServiceB`를 `@ComponentScan`으로 스캔합니다.
2. `ServiceA`의 생성자를 분석해 `ServiceB`가 필요하다는 것을 파악합니다.
3. 먼저 `ServiceB` 객체를 생성하고, 그 객체를 `ServiceA`의 생성자에 주입하여 `ServiceA`를 생성합니다.
4. **이 모든 과정이 애플리케이션 시작 시에 미리 수행됩니다.**

>[!Question]
>스프링 빈에 대해서 궁금한 점이 있어. @Component를 사용한 클래스가 있고, 패키지 최상위에서 ComponentScan을 하면 ComponentScan에 의해서 @Component를 설정한 클래스는 모두 빈으로 등록이 되잖아? 그러면 추후에 빈이 호출이 될 때 어떤 객체를 주입할지 미리 알고 있어야 하는데, 생성자 주입의 경우 객체를 생성할 때 딱 1번만 호출이 된다고 해. 여기서 궁금한 점은, 개발자가 new 키워드로 객체를 생성하지 않아도 ComponentScan에 의해서 미리 객체를 생성하고 객체간의 의존관계가 미리 맺어져 있는지 궁금해.

