# 다형성
>[!Note] 다형성(Polymorphism): 다양한 형태, 여러 형태
>프로그래밍에서 다형성은 **한 객체가 여러 타입의 객체로 취급될 수 있는 능력**을 뜻한다.
>다형성을 사용하면 하나의 객체가 다른 타입으로 사용될 수 있다는 뜻이다.

## 다형적 참조: 하나의 변수 타입으로 다양한 자식 인스턴스를 참조할 수 있는 기능
![[Pasted image 20241115154528.png|100|200]]
```java
package poly.basic;  
  
public class Parent {  
  
    public void parentMethod() {  
        System.out.println("Parent.parentMethod");  
    }  
}
```
```java
package poly.basic;  
  
public class Child extends Parent{  
  
    public void childMethod() {  
        System.out.println("Child.childMethod");  
    }  
}
```
```java
package poly.basic;  
  
public class PolyMain {  
    public static void main(String[] args) {  
        // 부모 변수가 부모 인스턴스 참조  
        System.out.println("Parent -> Parent");  
        Parent parent = new Parent();  
        parent.parentMethod();  
        
        // 자식 변수가 자식 인스턴스 참조  
        System.out.println("Child -> Child");  
        Child child = new Child();  
        child.parentMethod();  
        child.childMethod();  
        
        // 부모 변수가 자식 인스턴스 참조(다형적 참조)  
        System.out.println("Parent -> Child");  
        Parent poly = new Child(); // 부모 타입은 자식을 참조할 수 있다.  
        poly.parentMethod();  
        
//        Child child1 = new Parent(); // 자식은 부모를 담을 수 없다.  
//        poly.childMethod(); // 자식의 기능을 호출할 수 없다. 컴파일 오류 발생  
    }  
}
```
```
Parent -> Parent
Parent.parentMethod

Child -> Child
Parent.parentMethod
Child.childMethod

Parent -> Child
Parent.parentMethod
```
![[Pasted image 20241115155114.png]]
- `Parent poly = new Child();`
	- 부모 타입의 변수가 자식 인스턴스를 참조한다.
	- Child 인스턴스를 생성했기 때문에 메모리 상에 `Child`와 `Parent`가 모두 생성된다.
	- 생성된 참조값을 `Parent` 타입의 변수인 `poly`에 담아둔다.
- **다형적 참조**
	- 부모는 자식을 담을 수 있지만, **자식 타입은 부모 타입을 담을 수 없다.**
	- Parent 타입의 변수는 자신인 Parent는 물론이고, 자식 타입까지 참조할 수 있다.
	  **만약 손자가 있다면 그 손자도 그 하위 타입도 참조할 수 있다.**
		- Parent poly = new Parent();
		- Parent poly = new Child();
		- Parent poly = new Grandson(); -> **Child 하위에 손자가 있어도 가능**
- 다형적 참조와 인스턴스 실행
- `poly.parentMethod()`를 호출하면 참조값을 사용해서 인스턴스를 찾는다.
	- `poly`는 `Parent` 타입이므로 `Parent` 클래스부터 시작해서 필요한 기능을 찾는다.
	- `Parent` 클래스에 `parentMethod()`가 있으므로 해당 메서드가 호출된다.
- 다형적 참조의 한계
	- `Parent poly = new Child();` -> `poly.childMethod();` 호출 시 메모리 구조
	  ![[Pasted image 20241115155925.png]]
		1. `poly.childMethod()`를 실행하면 먼저 참조값을 통해 인스턴스를 찾는다.
		2. 인스턴스 안에서 실행할 타입을 찾아야 한다.
		3. 호출자인 `poly`는 `Parent` 타입이므로 `Parent` 클래스부터 시작해서 필요한 기능을 찾는다.
		4. 상속관계는 부모 방향으로 찾아 올라갈 수는 있지만, **자식 방향으로 찾아 내려갈 수는 없다.** `Parent`는 부모 타입이고, 상위에 부모가 없다.
		5. 따라서 `childMethod()`를 찾을 수 없으므로 **컴파일 오류가 발생한다.**
		6. 이럴 때는 **다운 캐스팅**을 통해 해결할 수 있다.
>[!Note] 다형적 참조의 핵심은 부모는 자식을 품을 수 있다는 것이다.
>



## 다형성과 캐스팅
![[Pasted image 20241115161640.png]]
- 위의 문제점을 다운 캐스팅으로 해결할 수 있다.
	- 호출하는 타입을 자식인 Child 타입으로 변경하면 인스턴스의 Child에 있는 childMethod()를 호출할 수 있다.
- 다운 캐스팅
	- `Child child = (Child) poly;`
	- poly는 Parent 타입인데, 이 타입을 (Child)를 사용해서 **일시적으로** 자식 타입인 child 타입으로 변경한다.
```
//poly.childMethod(); 불가능
Parent poly = new Child(); 

//다운캐스팅을 통해 부모타입을 자식 타입으로 변환한 다음에 대입 시도
Child child = (Child) poly

//참조값을 읽은 다음 자식 타입으로 지정
Child child = (Child) x001

//최종 결과
Child child = x001 
```


>[!Question] 다운캐스팅은 왜 개발자가 직접 명시적으로 캐스팅을 해야 할까?
### 다운캐스팅과 주의점
>[!Warning] 다운캐스팅은 잘못하면 심각한 런타임 오류가 발생할 수 있다.
```java
package poly.basic;  
  
// 다운캐스팅을 자동으로 하지 않는 이유  
public class Castingmain4 {  
  
    public static void main(String[] args) {  
        Parent parent1 = new Child();  
        Child child1 = (Child) parent1;  
        child1.childMethod(); // 문제 없음.  
  
        Parent parent2 = new Parent();  
        Child child2 = (Child) parent2; // 런타임 오류 - ClassCasting Exception        
        child2.childMethod(); // 실행 불가  
    }  
}
```
```
Child.childMethod
Exception in thread "main" java.lang.ClassCastException: class poly.basic.Parent cannot be cast to class poly.basic.Child (poly.basic.Parent and poly.basic.Child are in unnamed module of loader 'app')
	at poly.basic.Castingmain4.main(Castingmain4.java:12)
```
![[Pasted image 20241115163408.png]]
![[Pasted image 20241115163417.png]]
- `Parent parent2 = new Parent();`
	- `new Parent();`로 부모 타입으로 객체를 생성
	- **메모리 상에 자식 타입은 전혀 존재하지 않는다.**
- `Child child2 = (Child) parent2;`
	- `parent2`를 `Child` 타입으로 **다운캐스팅**한다.
	- 그런데 **`parent2`는 `Parent`로 생성이 되어있었다.**
	- 따라서 **메모리 상에 Child 자체가 존재하지 않기 때문에** 문제가 발생한 것이다.
	- 자바에서는 이렇게 사용할 수 없는 타입으로 다운캐스팅 하는 경우에 `ClassCastException`이라는 예외를 발생시킨다. (런타임 오류)


![[Pasted image 20241115164721.png]]

![[Pasted image 20241115164737.png]]
- `new B()`로 인스턴스를 생성하면, 인스턴스 내부에 자신과 부모인 A, B가 생성된다.
- 따라서 A, B 모두 B 인스턴스를 참조할 수 있다.
	- `A a = new B();` : A로 업케스팅
	- `B b = new B();` : 자신과 같은 타입
	- `C c = new B();` : 하위 타입은 대입할 수 없음, **컴파일 오류**
	- `C c = (C) new B()` : 하위 타입으로 **강제로 다운캐스팅**
		- 하지만, B인스턴스에 C와 관련된 부분이 없으므로 **잘못된 캐스팅**
		- `ClassCastException` **런타임 오류 발생.**

### instanceof
>[!Note] 어떤 인스턴스를 참조하고 있는지 확인하는 방법
>다운캐스팅을 수행하기 전에는 먼저 `instanceof`를 사용해서 원하는 타입으로 변경이 가능한지
>확인한 다음에 다운캐스팅을 하는게 안전하다.
```java
package poly.basic;  
  
public class Castingmain5 {  
    public static void main(String[] args) {  
  
        Parent parent1 = new Parent();  
        System.out.println("parent1 호출");  
        call(parent1);  
  
        Parent parent2 = new Child();  
        System.out.println("parent2 호출");  
        call(parent2);  
    }  
  
    private static void call(Parent parent) {  
        parent.parentMethod();  
        if (parent instanceof Child) {  
            System.out.println("Child 인스턴스 맞음");  
            Child child = (Child) parent;  
  
            child.childMethod();  
        }    }  
}
```

## 메서드 오버라이딩: 기존 기능을 하위 타입에서 새로운 기능으로 재정의
>[!Note] 오버라이딩 된 메서드가 항상 우선권을 가진다.
>기존 기능을 덮어 **새로운 기능을 재정의** 한다는 의미.

![[Pasted image 20241115173039.png|350|left|300]]
```java
package poly.overriding;  
  
public class Parent {  
  
    public String value = "parent";  
  
    public void method() {  
        System.out.println("Parent.method");  
    }  
}
```
```java
package poly.overriding;  
  
public class Child extends Parent{  
  
    public String value = "child";  
  
    @Override  
    public void method() {  
        System.out.println("Child.method");  
    }  
}
```
```java
package poly.overriding;  
  
public class OverridingMain {  
    public static void main(String[] args) {  
  
        // 자식 변수가 자식 인스턴스 참조  
        Child child = new Child();  
        System.out.println("Child -> Child");  
        System.out.println("value = " + child.value);  
        child.method();  
  
        Parent parent = new Parent();  
        System.out.println("Parent -> Parent");  
        System.out.println("value = " + parent.value);  
        parent.method();  
  
        // 부모 변수가 자식 인스턴스 참조  
        Parent poly = new Child();  
        System.out.println("Parnet -> Child");  
        System.out.println("value = " + poly.value); // 변수는 오버라이딩 X  
		poly.method(); // 메서드는 오버라이딩 !    
	}  
}
```
![[Pasted image 20241115173557.png]]
```
Child -> Child
value = child
Child.method
```



Parent -> Parent
![[Pasted image 20241115173625.png]]
```
Parent -> Parent
value = parent
Parent.method
```


![[Pasted image 20241115173757.png]]
```
Parnet -> Child
value = parent
Child.method
```
- poly 변수는 Poly 타입이다.
	- 따라서 `poly.value`, `poly.method()`를 호출하면 인스턴스의 Parent 타입에서 기능을 우선적으로 찾아서 실행한다.
	- `poly.value`: Parent 타입에 있는 value 값을 읽는다.
	  **(멤버 변수는 오버라이딩 되지 않는다.)**
	- `poly.method()`: `Parent` 타입에 있는 `method()`를 실행하려고 한다.
		- 그런데 하위 타입인 `Child.method()`가 오버라이딩 되어 있다.
		- **오버라이딩 된 메서드는 항상 우선권을 갖는다.**
		- 따라서 Parent.method()가 아니라, **Child.method()가 실행된다.**
- **오버라이딩 된 메서드는 항상 우선권을 가진다.**
	- 오버라이딩: 부모 타입에서 정의한 기능을 자식 타입에서 재정의하는 것.
	- 만약 자식에서도 오버라이딩하고, 손자에서도 같은 메서드를 오버라이딩 하면,
	  손자의 오버라이딩 메서드가 우선권을 가진다.
	  **더 하위 자식의 오버라이딩 된 메서드가 우선권을 가진다.**


<< 다형성을 어떻게 활용할 수 있을까? >>
[[h) 다형성 활용]]