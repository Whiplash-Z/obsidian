# 상속이 필요한 이유
```java
package extends1.ex1;  
  
public class ElectricCar {  
  
    public void move() {  
        System.out.println("차를 이동합니다.");  
    }  
  
    public void charge() {  
        System.out.println("충전합니다.");  
    }  
}
```
```java
package extends1.ex1;  
  
public class GasCar {  
  
    public void move() {  
        System.out.println("차를 이동합니다.");  
    }  
  
    public void fillUp() {  
        System.out.println("기름을 주유합니다.");  
    }  
}
```
```java
package extends1.ex1;  
  
public class CarMain {  
    public static void main(String[] args) {  
        ElectricCar electricCar = new ElectricCar();  
        electricCar.move();  
        electricCar.charge();  
  
        GasCar gasCar = new GasCar();  
        gasCar.move();  
        gasCar.fillUp();  
    }  
}
```
- 자동차(Car)는 전기차와 가솔린차를 포함하는 **추상적인 개념**이다.
- 전기차와 가솔린차는 자동차(Car)의 **구체적인 개념**이다.
- 전기차와 가솔린차는 이동(move)과기능이 **공통**이다.
- 이런 경우 상속 관계를 사용하는 것이 효과적이다.

# 상속 관계
>[!Note] 상속이란?
>상속은 객체 지향 프로그래밍의 핵심 요소 중 하나이다.
>기존 클래스의 **필드**와 **메서드**를 새로운 클래스에서 **재사용하게 해준다.**

```java
package extends1.ex2;  
  
public class Car {  
  
    public void move() {  
        System.out.println("차를 이동합니다.");  
    }  
}
```
```java
package extends1.ex2;  
  
public class ElectricCar extends Car{  
  
    public void charge() {  
        System.out.println("충전합니다.");  
    }  
    /**  
     * 1. 상속 관계의 객체를 생성하면 그 내부에는 부모와 자식이 모두 생성된다.  
     * 2. 상속 관계의 객체를 호출할 때, 대상 타입을 정해야 한다. 
       이때 호출자의 타입을 통해 대상 타입을 찾는다.  
     * 3. 현재 타입에서 기능을 찾지 못하면 상위 부모 타입으로 기능을 찾아서
       실행한다. 기능을 찾지 못하면 컴파일 오류가 발생한다.  
     */}
```
```java
package extends1.ex2;  
  
public class GasCar extends Car {  
  
    public void fillUp() {  
        System.out.println("기름을 주유합니다.");  
    }  
}
```
```java
package extends1.ex2;  
  
  
public class CarMain {  
  
    public static void main(String[] args) {  
        ElectricCar electricCar = new ElectricCar();  
        electricCar.move();  
        electricCar.charge();  
  
        GasCar gasCar = new GasCar();  
        gasCar.move();  
        gasCar.fillUp();  
    }  
}
```
![[Pasted image 20241115114521.png]]전기차와 가솔린차가 Car를 상속 받은 덕분에 move()를 **재사용**할 수 있다.

# 상속과 메모리 구조 ⭐️⭐️⭐️⭐️⭐️
```java
ElectricCar electricCar = new ElectricCar();
```
![[Pasted image 20241115121045.png]]
- `new ElectricCar()`를 호출하면 `ElectricCar`뿐만 아니라, 상속 관계에 있는 `Car`까지 **포함해서 인스턴스를 생성한다.**
- 상속이라고 해서 단순하게 **부모의 필드와 메서드만 물려 받는게 아니다.**
- 부모와 자식이 **모두 생성**되고 **공간도 구분**된다.



```java
electricCar.charge();
```
![[Pasted image 20241115121331.png]]
- `electricCar.charge()`를 호출하면 **호출하는 변수의 타입(클래스)을 기준으로 선택**한다.
- `electricCar` 변수의 타입이 `ElectricCar`이므로 인스턴스 내부에 **같은 타입**인 `ElectricCar`를 통해서 `charge()`를 호출한다.


```java
electricCar.move();
```
![[Pasted image 20241115121620.png]]
1. 먼저 호출하는 변수인 `electricCar`의 타입이 `ElectricCar`이므로 이 타입을 선택한다.
2. 그런데 `ElectricCar`에는 `move()`메서드가 없다.
3. 상속 관계에서는 **자식 타입에 해당 기능이 없으면 부모 타입으로 올라가서 찾는다.**
4. `ElectricCar`의 부모인 `Car`로 올라가서 `move()`를 찾는다.
5. `Car`에 `move()`가 있으므로 부모에 있는 `move()` 메서드를 호출한다.

# 상속과 메서드 오버라이딩(재정의)
>[!Note] 메서드 오버라이딩
>부모에게서 상속 받은 기능을 **자식이 재정의 하는 것**을 **메서드 오버라이딩(Overriding)**이라 한다.

```java
package extends1.overriding;  
  
public class Car {  
  
    public void move() {  
        System.out.println("차를 이동합니다.");  
    }  
    
    // 추가  
    public void openDoor() {  
        System.out.println("문을 엽니다.");  
    }  
}
```
```java
package extends1.overriding;  
  
public class ElectricCar extends Car {  
  
    @Override  
    public void move() {  
        System.out.println("전기차를 빠르게 이동합니다.");  
    }  
    
    public void charge() {  
        System.out.println("충전합니다.");  
    }  
```
- ElectricCar는 부모인 Car의 move() 기능을 그대로 사용하고 싶지 않다.
- 그래서 ElectricCar의 move() 메서드를 새로 만들었다.
- 이렇게 **부모의 기능을 자식이 새로 재정의 하는 것**을 **메서드 오버라이딩**이라 한다.

>[!Note] @Override
>`@`이 붙은 부분을 애노테이션이라 한다.
>애노테이션은 주석과 비슷한데, 프로그램이 읽을 수 있는 특별한 주석이다.
>
>이 애노테이션은 상위 클래스의 메서드를 오버라이드하는 것임을 의미한다.
>**컴파일러는 이 애노테이션을 보고 메서드가 정확히 오버라이드 되었는지 확인한다.**
>오버라이딩 조건을 만족시키지 않으면 컴파일 에러를 발생시킨다.


## 오버라이딩과 메모리 구조
![[Pasted image 20241115141624.png]]
![[Pasted image 20241115141642.png]]
1. electricCar.move() 호출
2. electricCar의 타입은 ElectricCar 이므로 인스턴스 내부의 ElectricCar 타입에서 시작.
3. ElectricCar 타입에 move() 메서드가 있으므로 **해당 메서드를 실행한다.**
4. 실행할 메서드를 찾았으므로 **부모 타입을 찾지 않는다.**


## 메서드 오버라이딩 조건
![[Pasted image 20241115141918.png]]

# super: 부모참조와 생성자

## 부모 참조
>[!Note] Super - 부모 참조
>**부모와 자식의 필드명이 같거나 메서드가 오버라이딩 되어 있으면**, 자식에서 부모의 필드나 메서드를 **호출할 수 없다.**
>이때 `super` 키워드를 사용하면 **부모를 참조할 수 있다.**

```java
package super1;  
  
public class Parent {  
  
    public String value = "parent";  
  
    public void hello() {  
        System.out.println("Parent.hello");  
    }  
}
```
```java
package super1;  
  
public class Child extends Parent{  
  
    public String value = "child";  
  
    @Override  
    public void hello() {  
        System.out.println("Child.hello");  
    }  
  
    public void call() {  
        System.out.println("this value = " + this.value); // this 생략 가능  
        System.out.println("super value = " + super.value);  
  
        this.hello();  
        super.hello();  
    }  
}
```
```java
package super1;  
  
public class Super1Main {  
    public static void main(String[] args) {  
        Child child = new Child();  
        child.call();  
    }  
}
```

```
this value = child
super value = parent
Child.hello
Parent.hello
```

>필드 이름과 메서드의 이름이 부모와 같지만, `super` 를 사용해서 부모 클래스에 있는 기능을 사용할 수 있다.

### super 메모리 그림
![[Pasted image 20241115143242.png]]

## 생성자
>[!Note] super - 생성자
>상속 관계의 인스턴스를 생성하면 결국 메모리 내부에는 자**식과 부모 클래스가 각각 다 만들어진다.** Child를 만들면 부모인 Parent까지 함께 만들어지는 것이다.
>따라서 각각의 **생성자도 모두 호출되어야 한다.**

>[!Warning] 규칙
>상속 관계를 사용하면 **자식 클래스의 생성자에서 부모 클래스의 생성자를 반드시 호출해야 한다.**

```java
package super2;  
  
public class ClassA {  
  
    public ClassA() {  
        System.out.println("ClassA 생성자");  
    }  
}
```
```java
package super2;  
  
public class ClassB extends ClassA {  
  
    public ClassB(int a) {  
        super(); // 기본 생성자 생략 가능  
        System.out.println("ClassB 생성자 a = " + a);  
    }  
  
    public ClassB(int a, int b) {  
        super(); // 기본 생성자 생략 가능  
        System.out.println("ClassB 생성자 = a" + a + " b = " + b);  
  
    }  
}
```
```java
package super2;  
  
public class ClassC extends ClassB {  
  
    public ClassC() {  
        super(10, 20);  
        System.out.println("ClassC 생성자");  
    }  
}
```
```java
package super2;  
  
public class Super2Main {  
  
    public static void main(String[] args) {  
  
        ClassC classC = new ClassC();  
  
    }  
}
```
```
ClassA 생성자
ClassB 생성자 = a10 b = 20
ClassC 생성자
```


- ClassB는 ClassA를 상속 받았다.
	- **상속을 받으면 자식의 생성자의 첫줄에 super(...)를 사용해서 부모 클래스의 생성자를 호출해야 한다.**
	- 생성자 첫줄에 this(...)를 사용할 수는 있지만, super(...)는 자식의 생성자 안에서 반드시 한번은 호출해야 한다.
- **부모 클래스의 생성자가 기본 생성자인 경우에는 super()를 생략할 수 있다.**
	- 상속 관계에서 첫줄에 super(...)를 생략하면 자바는 부모의 기본 생성자를 호출하는 super()를 자동으로 만들어준다.

![[Pasted image 20241115145334.png]]

- 상속 관계의 생성자 호출은 **결과적으로 부모에서 자식 순서로 실행된다.**
	- 따라서 **부모의 데이터를 먼저 초기화**하고, 그 다음에 **자식의 데이터를 초기화** 한다.
- 상속 관계에서 **자식 클래스의 생성자 첫줄에 반드시 super(...)를 호출해야 한다.**
	- 단, 기본 생성자(super())인 경우 생략할 수 있다.
	- 코드의 첫 줄에 `this(...)`를 사용하더라도 반드시 한번은 `super(...)`를 호출해야 한다.