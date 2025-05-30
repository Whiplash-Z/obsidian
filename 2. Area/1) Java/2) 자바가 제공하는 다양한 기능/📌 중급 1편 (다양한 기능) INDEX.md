- [[Object 클래스]]
	- 자바에서 모든 클래스의 최상위 부모는 항상 Object 클래스이다.
		- 공통 기능 제공(toString, equals, getClass ...) -> 공통 기능 사용 **일관성** ⭕️
		- 다형성의 기본 구현: 다양한 타입의 객체를 **통합적으로 처리**할 수 있게 된다.
	- Object 다형성의 장점과 단점 그리고 한계
		- 모든 객체를 대상으로 **다형적 참조를 할 수 있다.**(모든 객체를 담을 수 있다)
		- Object를 통해 전달 받은 객체를 호출하려면 각 객체에 맞는 **다운캐스팅 과정 필요**
		- 다형적 참조는 가능하지만, Object에는 dog.sound(), car.move()와 같은 메서드가 존재하지 않아서(공통 기능 제외) 메서드 오버라이딩이 안되기 때문에 **다형성을 활용하기에는 한계가 있다.**
			결국 각 객체의 기능을 호출하려면 다운캐스팅을 해야한다.
	- Object를 활용하면 좋은 상황
		- Object 배열: 모든 객체를 담을 수 있는 배열
		- toString(): 객체의 정보를 문자열 형태로 제공 (디버깅, 로깅에서 유용하게 사용)
	- Object와 OCP
		- Open: 새로운 클래스를 추가하고 toString()을 **오버라이딩해서 기능 확장 가능**
		- Closed: 새로운 클래스를 추가해도 Object와 toString()을 사용하는 클라이언트 코드인 ObjectPrinter는 **변경하지 않아도 된다.**
		- 클라이언트 코드가 구체적인 Car, Dog에 의존하는 것이 아닌,
		  **추상적인 Object에 의존**하기 때문에 OCP 원칙을 지킬 수 있었다.
	- equals() 동일성과 동등성
		- 동일성: **물리적**으로 같은 메모리에 있는 객체 인스턴인지 **참조값을 확인**하는 것.
				  == 비교
		- 동등성: **논리적**으로 같은지 확인하는 것. (회원 번호, 주민등록 번호)
					`equals()`메서드를 사용해서 비교
			- 각 클래스마다 동등성의 개념이 다르기 때문에 **equals()를 재정의 해야 한다.**
			  그렇지 않으면 **Object는 동일성 비교를 기본으로 제공한다.**
- [[불변 객체]]
	- 객체의 상태(객체 내부의 값, 필드, 멤버 변수)가 변하지 않는 객체
	- 기본형과 참조형의 공유
		- 기본형: 하나의 값을 여러 변수에서 **절대로 공유하지 않는다.**
		- 참조형: 하나의 객체를 참조값을 통해 여러 변수에서 **공유할 수 있다.**
	- 공유 참조와 사이드 이펙트
		- a,b는 같은 인스턴스를 참조하기 때문에 b의 값을 변경할 때
		  **a의 값도 부산으로 변경된다.**
		- 참조값의 공유를 막을 수 있는 방법이 없다.
	- 해결 방법
		- a와 b가 처음부터 **서로 다른 인스턴스를 참조**하면 된다.
		- 불변 객체(Immutable Object) 도입
			- 문제의 원인
				- 객체를 공유하는 것 자체는 문제가 아니다.
				- **공유된 객체의 값을 변경한 것이 문제다.**
			- 불변 객체: 객체의 상태가 변하지 않는 객체
			- 값을 변경할 수 없도록 `final` 키워드 선언
				- 값을 변경할 수 있는 **setter 메서드 제거**
				- **생성자를 통해서만 값을 변경**
				- 이후에는 **값을 변경하는 것이 불가능**
			- 사이드 이펙트가 발생하면 안되는 상황이라면
			  불변 객체를 만들어서 사용하면 된다.
			- 불변 객체의 **값을 변경하고 싶다면**
			  변경하고 싶은 값으로 **새로운 불변 객체를 생성해야 한다.**
	- 클래스를 불변으로 설계하는 이유 (지금은 원리를 이해하는 데 집중!)
		- 캐시 안정성
		- 멀티 쓰레드 안정성
		- 엔티티의 값 타입
- [[String 클래스]]
	- String 클래스는 내부 문자열 값을 비교하도록 **equals() 메서드를 재정의** 해두었다.
		- 문자열 비교는 항상 `equals()`로 비교해야 한다.
	- String은 불변 객체이므로 생성 이후에 절대로 내부의 문자열 값을 변경할 수 없고,
	  변경이 필요한 경우 기존 값을 변경하지 않고, **새로운 결과를 만들어서 반환한다.**
		- 불변 객체로 설계되지 않았다면, 문자열 풀에서 같은 문자를 참조하는 변수의 모든 문자가 변경되는 사이드 이펙트가 발생했을 것이다.
	- StringBuilder
		- 불변인 String 클래스의 단점
			- 문자를 더하거나 변경할 때 마다 **계속해서 새로운 객체를 생성해야 한다.**
		- StringBuilder는 가변적이다.
			- 하나의 StringBuilder 객체 안에서 문자열을 추가, 삭제, 수정할 수 있고,
			  이때마다 **새로운 객체를 생성하지 않는다.**
			- StringBuilder는 보통 문자열을 변경하는 동안만 사용하다가 문자열 변경이 끝나면 안전한(불변) String으로 변경하는 것이 좋다.
- 래퍼(Wrapper) 클래스
	- 기본형의 한계
		- 객체가 아니다.
			- 객체 지향 프로그래밍의 장점을 살릴 수 없다.
				- 객체는 유용한 메서드를 제공할 수 있는데, **기본형은 메서드 제공 불가**
				- 객체 참조가 필요한 컬렉션 프레임워크와 제네릭을 사용할 수 없다.
		- null 값을 가질 수 없다.
			- **데이터가 "없음" 이라는 상태**를 나타내야할 필요가 있는데, 기본형은 항상 값을 가지기 때문에 **이런 표현을 할 수가 없다.**
	- 래퍼 클래스
		- 기본형을 객체로 감싸서 더 편리하게 사용하도록 도와주기 때문에 유용하다.
		- 불변이다.
		- 래퍼 클래스는 내부의 값을 비교하도록 **equals() 를 재정의 해두었다.**
		  따라서 값을 비교하려면 `equals()` 를 사용해야 한다.
		  -> 래퍼 클래스는 불변이므로, 새로 생성된 객체의 참조값을 비교하면 당연히 false가 발생한다.
	- 성능 비교
		- 기본형 연산이 래퍼 클래스보다 대략 5배 정도 빠른 것을 확인할 수 있다.
		- 기본형은 메모리에서 단순히 그 크기만큼의 공간을 차지한다.
			- 예를 들어 int 는 보통 4바이트의 메모리를 사용한다.
		- ![[Pasted image 20241124004929.png]]
- [[열거형(ENUM)]]
	- 문자열을 사용하는 방식의 문제점
		- 타입 안정성 부족: **컴파일 시 오류 감지 불가**(오타, 유효하지 않은 값에 대해)
		- 데이터 일관성 ❌: "GOLD", "gold", "Gold" 등 다양한 형식으로 문자열을 입력할 수 있어서 일관성이 떨어진다.
	- 타입 안전 열거형 패턴 [[열거형(ENUM)#^7552df]]
		- 타입 안정성 향상: 정해진 객체만 사용할 수 있기 때문에 잘못된 값을 입력하는 문제를 컴파일 시점에 방지할 수 있다. (제한된 인스턴스 생성)
		- 데이터 일관성: 정해진 객체만 사용하므로 데이터의 일관성을 보장한다.
- 날짜와 시간
	- Local
		- LocalDate: 날짜만 표현할 때 사용 (년,월,일 = 2024-11-24)
		- LocalTime: 시간만 표현할 때 사용 (시,분,초 = 08:20:30.213)
		- LocalDateTime: LocalDate와 LocalTime을 합한 개념(2024-11-24T08:20:30.213)
	- ZonedDateTime vs OffsetDateTime -> Global 서비스에서 사용
		- ZonedDateTime: 시간대를 고려한 날짜와 시간을 표현할 때 사용
			- 2013-11-21T08:20:30.213+9:00\[Asia/Seoul\]
				- `+9:00`은 UTC(협정 세계시)로 부터의 시간대 차이 = **오프셋**이라 한다.
				- `Asia/Seoul`은 **타임존**이라 한다.
				- 일광 절약 시간제가 적용된다.
			- 사용자 지정 시간대에 따른 **시간 계산이 필요할 때 적합**하다.
		- OffsetDateTime: 시간대를 고려한 날짜와 시간을 표현할 때 사용
			- 타임존은 없고, UTC로 부터의 시간대 차이인 고정된 오프셋만 포함한다.
			- 2013-11-21T08:20:30.213+9:00
			- 일광 절약 시간제가 적용되지 않는다.
			- **시간대 변환 없이** 로그를 기록하고, 데이터를 저장하고 처리할 때 적합하다.
	- Instant
		- UTC(협정 세계시)를 기준으로 하는 시간의 한 지점을 나타낸다.
		- 날짜와 시간을 나노초 정밀도로 표현하며,
		  **1970년 1월 1일 0시 0분 0초(UTC)를 기준으로 경과한 시간**으로 계산된다.
		- **Instant 내부에는 초 데이터만 들어있다.(나노초 포함)**
		- Epoch 시간을 다루는 클래스
			- UTC부터 현재까지 경과된 시간을 초 단위로 표현
			- 시간대에 영향을 받지 않는 절대적인 시간 표현 방식
		- 장점: UTC가 기준이므로 전 세계 어디서나 동일한 시점을 가리킨다.
		- 단점: 날짜와 시간을 계산하고 사용하는데 필요한 기능이 부족하다.
		- ![[Pasted image 20241124121449.png]]
	- Period
		- 특정 시점의 시간(시각)
			- 이 프로젝트는 2025년 5월 15일까지 완료해야해.
			- 내 생일은 8월 29일이야.
		- 두 날짜 **사이의 간격을 년,월,일 단위**로 나타낸다.
	- Duration
		- 시간의 간격(기간)
			- 앞으로 5개월은 더 공부해야 해.
			- 점심 시간까지 2시간 남았어.
		- 두 시간 **사이의 간격을 시,분,초(나노초) 단위**로 나타낸다.
	- ![[Pasted image 20241124121746.png]]
- [[중첩 클래스, 내부 클래스]]
	- 중첩 클래스: 클래스 안에 클래스를 중첩해서 정의된 클래스
		- 정접 중첩 클래스(Nested): 바깥 클래스와 밀접한 관련이 있지만, 인스턴스 간에 데이터 공유가 필요 없을 때 사용
		- 내부 클래스(Inner): 바깥 클래스의 인스턴스와 연결되어 있고, 바깥 클래스의 인스턴스 상태에 의존하거나 강하게 연결된 작업을 수행할 때 사용
			- 내부 클래스: 바깥 클래스의 인스턴스의 멤버에 접근
			- 지역 클래스: 내부 클래스의 특징 + 지역 변수에 접근
				- 지역 변수 캡처: 지역 클래스가 사용하는 **지역 변수를 복사**
				- 지역 변수와 지역 클래스를 통해 생성한 인스턴스의 생명주기가
				  다른 문제를 해결
				- 지역 클래스가 접근하는 지역 변수는 **절대로 중간에 값이 변하면 안된다.**
				  (사실상 final)
			- 익명 클래스: 지역 클래스의 특징 +클래스의 이름이 없는 특별한 클래스
				- 클래스의 이름을 생략하고, 클래스의 **선언과 생성을 한번에 처리**할 수 있다.
				- **지역 클래스가 일회용으로 사용되는 경우**나 간단한 구현을 제공할 때 사용한다.
	- **특정 클래스가 다른 하나의 클래스 안에서만 사용**되거나, **둘이 아주 긴밀하게 연결되어 있는 특별한 경우에만 사용**해야 한다.
- [[예외 처리]] :  정상 흐름과 예외 흐름을 구분하기 위해 사용한다.
	- Throwable: 최상위 예외
	- Error
		- 메모리 부족이나 심각한 시스템 오류와 같이 애플리케이션에서 복구가 불가능한 시스템 에외
		- 애플리케이션 개발자는 이 예외를 잡으려고 해서는 안된다.
	- Exception: 체크 예외
		- 개발자가 명시적으로 처리하지 않으면 컴파일 에러 발생
	- RuntimeException: 언체크 예외, 런타임 예외
		- 명시적으로 처리하지 않아도 컴파일 에러가 발생하지 않는다.
	- 예외처리를 사용하면 정상 흐름과 예외 흐름을 분리할 수 있는 장점이 있다.
	- **처리할 수 없는 예외들**은 중간에 여러곳에서 나누어 처리하기 보다는 예외를 공통으로 처리할 수 있는 곳을 만들어서 **한 곳에서 해결**하면 된다.
	- 어차피 해결할 수 없는 예외들이기 때문에 이런 경우 `고객에게는 현재 시스템에 문제가 있습니다.` 라고 **오류 메시지**를 보여주고, 만약 웹이라면 **오류 페이지**를 보여주면 된다. 그리고 내부 개발자가 지금의 문 제 상황을 빠르게 인지할 수 있도록, 오류에 대한 **로그를 남겨두면 된다.** 이런 부분은 **공통 처리**가 가능하다.