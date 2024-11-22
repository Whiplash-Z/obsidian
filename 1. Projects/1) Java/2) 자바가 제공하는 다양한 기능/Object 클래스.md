>[!Check] 자바에서 모든 클래스의 최상위 부모는 항상 Object 클래스이다.

![[Pasted image 20241119142011.png]]

![[Pasted image 20241119142934.png]]
```java
public class ObjectMain {  
  
    public static void main(String[] args) {  
        Child child = new Child();  
        child.childMethod();  
        child.parentMethod();  
  
        // toString()은 Object 클래스의 메서드  
        String string = child.toString();  
        System.out.println("string = " + string);  
    }  
}
```
```
Child.childMethod
Parent.parentMethod
string = lang.object.Child@452b3a41
```

# Object 클래스가 최상위 부모 클래스인 이유
1. 공통 기능 제공
	- 객체의 정보 제공, 객체 비교, 어떤 클래스로 만들어졌는지 확인 기능은
	  모든 객체에게 필요한 기본 기능이다.
	  이런 기능을 객체를 만들 때 마다 항상 새로운 메서드를 정의해서 만든다면 상당히 **번거로운 일**이 될 것이다.
	- 만든다고 해도 개발자마다 서로 다른 이름의 메서드를 만들기 때문에 **일관성이 없다**.
		- toString(): 객체의 정보를 제공
		- equals(): 객체의 같음을 비교
		- getClass(): 객체의 클래스 정보를 제공
		- 등등 ...
2. 다형성의 기본 구현
	- Object는 모든 클래스의 부모 클래스이므로 모든 객체를 참조할 수 있다.
	- 모든 자바 객체는 Object 타입으로 처리될 수 있기 때문에 다양한 타입의 객체를 통합적으로 처리할 수 있게 해준다.

# Object 다형성
![[Pasted image 20241119143854.png]]
```java
public class ObjectPolyExam {  
  
    public static void main(String[] args) {  
  
        Car car = new Car();  
        Dog dog = new Dog();  
  
        action(car);  
        action(dog);  
  
    }  
  
    private static void action(Object object) {  
//        object.sound(); // 컴파일 오류, Object는 sound()가 없음 !
//        object.move(); // 컴파일 오류, Object는 move()가 없음 !  
        if (object instanceof Dog dog) {  
            dog.sound();  
        } else if (object instanceof Car car) {  
            car.move();  
        }  
    }  
}
```
- Object 다형성의 장점
	- action(Object object) {...}
	- Object 타입의 매개변수를 사용하기 때문에 어떤 객체든 인자로 전달 가능
- Object 다형성의 한계
![[Pasted image 20241119144540.png]]
- action 메서드 안에서 object.sound()를 호출하면 오류 발생
	- Object에는 sound 메서드가 없다.
	- Object는 최종 부모이므로 더는 올라가서 찾을 수 없다.
- Dog 인스턴스의 sound()를 호출하려면 다운캐스팅을 해야한다.
![[Pasted image 20241119144751.png]]
- Object는 모든 객체를 대상으로 **다형적 참조를 할 수 있다.** (모든 객체를 담을 수 있다.)
- Object를 통해 전달 받은 객체를 호출하려면 **각 객체에 맞는 다운캐스팅 과정이 필요하다.**
- 다형적 참조는 가능하지만, Object에는 dog.sound(), car.move()와 같은 메서드가 존재하지 않아서(공통 기능 제외) 메서드 오버라이딩이 안되기 때문에 **다형성을 활용하기에는 한계가 있다.**
  결국 각 객체의 기능을 호출하려면 다운캐스팅을 해야한다.

>[!Question] Object를 언제 활용하면 좋을까?

## Object 배열
```java
public class ObjectPolyExample2 {  
    public static void main(String[] args) {  
        Dog dog = new Dog();  
        Car car = new Car();  
        Object object = new Object();  
  
        Object[] objects = {dog, car, object};  
  
        size(objects);  
    }  
  
    private static void size(Object[] objects) {  
        System.out.println("전달된 객체의 수는: " + objects.length);  
    }  
}
```
![[Pasted image 20241119151646.png]]

## toString() 오버라이딩
```java
public String toString() {
	return getClass().getName() + "@" + Integer.toHexString(hashCode()); 
}
```
>Object 가 제공하는 toString() 메서드는 기본적으로 패키지를 포함한 객체의 이름과 객체의 참조값(해시 코드)를 16진수로 제공한다.
```java
public class Dog {  
  
    private String dogName;  
    private int age;  
  
    public Dog(String dogName, int age) {  
        this.dogName = dogName;  
        this.age = age;  
    }  
  
    @Override  
    public String toString() {  
        return "Dog{" +  
                "dogName='" + dogName + '\'' +  
                ", age=" + age +  
                '}';  
    }  
}
```

참고: 객체의 참조값 출력
(toString()이나 hashCode()를 재정의한 경우 객체의 참조값 출력 불가능할 때 아래의 방법을 사용하면 된다.)
```java
String refValue = Integer.toHexString(System.identityHashCode(dog1)); System.out.println("refValue = " + refValue);
```


# equals() 동일성과 동등성
>[!Note] Object는 동등성 비교를 위한 equals() 메서드를 제공한다.
>- 동일성(Identity): == 연산자를 사용해서 두 객체의 참조가 동일한 객체인지 확인
>- 동등성(Equality): equals() 메서드를 사용해서 두 객체가 논리적으로 동등한지 확인

- 단어 정리
	- "동일"은 완전히 같음을 의미한다.
	- "동등"은 같은 가치나 수준을 의미하지만 형태나 외관 등이 완전히 같지 않을 수 있다.
- 동일성: 물리적으로 같은 메모리에 있는 객체 인스턴인지 참조값을 확인하는 것.
- 동등성: 논리적으로 같은지 확인하는 것.

```java
public class EqualsMainV1 {  
  
    public static void main(String[] args) {  
        UserV1 user1 = new UserV1("id-100"); // x001
        UserV1 user2 = new UserV1("id-100"); // x002
  
        System.out.println("identity = " + (user1 == user2));  
        System.out.println("identity = " + (user1.equals(user2)));  
    }  
}
```
```
identity = false
identity = false
```
- 이 경우 물리적으로 다른 메모리에 있는 다른 객체이지만,
  회원 번호 기준으로 생각해보면 논리적으로는 같은 회원으로 볼 수 있다.
  따라서 **동일성은 다르지만**, **동등성은 같다.**
- 그렇다면 동등성(equals) 비교를 했을 때 true라고 나와야 할 것 같은데 왜 false일까?
>[!Check] Object가 기본으로 제공하는 equals()는 \==으로 동일성 비교를 제공한다.
```java
public boolean equals(Object obj) {
	return (this == obj); 
}
```
- 동등성이라는 개념은 각각의 클래스 마다 다르다.
	- 어떤 클래스는 **주민등록번호를 기준으로 동등성을 처리**할 수 있고,
	- 어떤 클래스는 **고객의 연락처를 기준으로 동등성을 처리**할 수 있다.
- 따라서 동등성 비교를 사용하고 싶으면 **equals() 메서드를 재정의** 해야한다.
  그렇지 않으면, **Object는 동일성 비교를 기본으로 제공한다.**

```java
@Override  
public boolean equals(Object object) {  
    if (this == object) return true;  
    if (object == null || getClass() != object.getClass()) return false;  
    UserV2 userV2 = (UserV2) object;  
    id.equals(userV2.id);  
    return Objects.equals(id, userV2.id);  
}
```
- Object 의 equals() 메서드를 재정의했다.
- UserV2 의 동등성은 id (고객번호)로 비교한다.
- equals() 는 Object 타입을 매개변수로 사용한다.
  따라서 객체의 특정 값을 사용하려면 다운캐스팅이 필요하다
- 현재 인스턴스에 있는 id 문자열과 비교 대상으로 넘어온 객체의 id 문자열을 비교한다.
- UserV2 에 있는 id 는 String 이다.
  문자열 비교는 == 이 아니라 equals() 를 사용해야 한다.

```java
public class EqualsMainV2 {  
    public static void main(String[] args) {  
  
        UserV2 user1 = new UserV2("id-100");  
        UserV2 user2 = new UserV2("id-100");  
  
        System.out.println("identity = " + (user1 == user2));  
        System.out.println("equality = " + user1.equals(user2));  
  
    }  
}
```
```
identity = false
equality = true
```
- 객체의 참조가 다르므로 동일성은 다르다.
- user1,user2는 서로 다른 객체이지만, 둘다 같은 id(고객번호)를 가지고 있으므로 동등하다. (동일성)

