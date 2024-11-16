# 다형성 활용
![[Pasted image 20241116114153.png]]
```java
package poly.ex;  
  
public class Dog {  
  
    public void sound() {  
        System.out.println("멍멍 !");  
    }  
}
```
```java
package poly.ex;  
  
public class Cat {  
  
    public void sound() {  
        System.out.println("야옹~");  
    }  
}
```
```java
package poly.ex;  
  
public class Caw {  
  
    public void sound() {  
        System.out.println("음매 ~~");  
    }  
}
```
```java
package poly.ex;  
  
public class AnimalSoundMain {  
    public static void main(String[] args) {  
        Dog dog = new Dog();  
        Cat cat = new Cat();  
        Caw caw = new Caw();  
  
        soundDog(dog);  
        soundCat(cat);  
        soundCaw(caw);  
  
  
    }  
  
    private static void soundDog(Dog dog) {  
        System.out.println("동물 소리 테스트 시작");  
        dog.sound();  
        System.out.println("동물 소리 테스트 종료");  
    }  
  
    private static void soundCat(Cat cat) {  
        System.out.println("동물 소리 테스트 시작");  
        cat.sound();  
        System.out.println("동물 소리 테스트 종료");  
    }  
  
    private static void soundCaw(Caw caw) {  
        System.out.println("동물 소리 테스트 시작");  
        caw.sound();  
        System.out.println("동물 소리 테스트 종료");  
    }  
}
```
- 문제점
	- 중복된 코드가 있다.
	- **새로운 동물이 추가될 때마다** 더 많은 **중복 코드를 작성해야 한다.**
	- **타입이 서로 다르기 때문에** 중복 제거를 할 수가 없다.
- 해결 방법
	- 다형성의 **다형적 참조**와 **메서드 오버라이딩**을 활용하면
	  **모두 같은 타입을 사용**하고, **각자 자신의 메서드도 호출**할 수 있다.
![[Pasted image 20241116114727.png]]
- 다형성을 사용하기 위해 여기서는 상속 관계를 사용한다.
- Animal(동물)이라는 부모 클래스를 만들고 sound() 메서드를 정의한다.

```java
package poly.ex2;  
  
public class Animal {  
  
    public void sound() {  
        System.out.println("동물 울음 소리");  
    }  
}
```
```java
package poly.ex2;  
  
public class Dog extends Animal{  
    @Override  
    public void sound() {  
        System.out.println("멍멍 !");  
    }  
}
```
```java
package poly.ex2;  
  
public class Cat extends Animal{  
    @Override  
    public void sound() {  
        System.out.println("야옹 ~");  
    }  
}
```
```java
package poly.ex2;  
  
public class Caw extends Animal{  
    @Override  
    public void sound() {  
        System.out.println("음머 ~~");  
    }  
}
```

```java
package poly.ex2;  
  
public class AnimalPolyMain1 {  
    public static void main(String[] args) {  
        Dog dog = new Dog();  
        Cat cat = new Cat();  
        Caw caw = new Caw();  
        Duck duck = new Duck();  
  
        soundAnimal(dog);  
        soundAnimal(cat);  
        soundAnimal(caw);  
        soundAnimal(duck);  
    }  
    
    public static void soundAnimal(Animal animal) {  
        System.out.println("동물 소리 테스트 시작");  
        animal.sound();  
        System.out.println("동물 소리 테스트 종료");  
    }  
}
```
![[Pasted image 20241116115306.png]]
1. `soundAnimal(...)`메서드의 매개변수 타입은 `Animal` 이므로 상속받은 `Dog`, `Cat`, `Caw`가 인자로 허용된다. **Animal animal = (dog, cat, caw)와 같다.**
2.  `animal` 변수의 타입은 `Animal` 이므로, `Dog` 인스턴스에 있는 `Animal` 클래스의 부분을 찾아서 `sound()` 메서드 호출을 시도한다.
3.  그런데 하위 클래스인 `Dog`에서 `sound()` 메서드를 오버라이딩 했기 때문에 **오버라이딩한 메서드가 우선권을 가진다.**
4.  `Dog` 클래스에 있는 `sound()` 메서드가 호출되므로 "멍멍 !"이 출력된다.
5.  이때, `Animal`의 `sound()`는 실행되지 않는다.


>[!Note] 이 코드의 핵심은 Animal animal 부분이다.
>- **다형적 참조** 덕분에 animal 변수는 **자식**인 Dog, Cat, Caw의 **인스턴스를 참조**할 수 있다.
>- **메서드 오버라이딩** 덕분에 animal.sound()를 호출해도 dog.sound, cat.sound(), caw.sound()와 같이 **각 인스턴스의 메서드를 호출할 수 있다.**

```java
package poly.ex2;  
  
public class AnimalPolyMain3 {  
    public static void main(String[] args) {  
        Animal[] animalArr = {new Dog(), new Cat(), new Caw(), new Duck()};  
  
        for (Animal animal : animalArr) {  
            soundAnimal(animal);  
        }    }  
  
    // 변하지 않는 부분  
    private static void soundAnimal(Animal animal) {  
        System.out.println("동물 소리 테스트 시작 !");  
        animal.sound();  
        System.out.println("동물 소리 테스트 종료 !");  
    }  
}
```
>[!Success] 다형성 덕분에 재사용이 가능해졌다.
>이후에 새로운 동물(Duck)을 추가해도 **코드를 그대로 재사용 할 수 있다.**

---


>[!Warning] 남은 문제
>1. Animal 클래스를 생성할 수 있는 문제
>2. Animal 클래스를 상속 받는 곳에서 sound() 메서드를 오버라이딩 하지 않을 가능성

## Animal 클래스를 생성할 수 있는 문제 ^canCreateClass
>[!Warning] Animal 클래스는 동물이라는 **추상적인 클래스**다.
>이 클래스를 `Animal animal = new Animal();` 처럼 직접 생성해서 사용할 일이 있을까?
- 강아지, 고양이, 소는 실제 존재하는 것이 당연하지만, **동물이라는 추상적인 개념이 실제로 존재하는 것은 이상하다.**
- 이 클래스는 **다형성을 위해서 필요한 것**이지, **직접 인스턴스를 생성해서 사용할 일은 없다.**

- 제약이 없을 경우에 발생하는 문제
	1. Animal도 클래스이기 때문에 인스턴스를 생성하고 **사용하는데 아무런 제약이 없다.**
	2. 실수로 `new Animal();`을 사용해서 Animal의 **인스턴스를 생성해도 문제되지 않는다.**
	3. 이렇게 생성된 인스턴스는 작동은 하지만 **제대로된 기능을 수행하지는 않는다.**


## Animal 클래스를 상속 받는 곳에서 sound() 메서드를 오버라이딩 하지 않을 가능성 ^possibilityOfNotOverriding
>[!Note] 좋은 프로그램은 제약이 있는 프로그램이다.
- Animal을 상속 받은 Pig 클래스를 만든다고 가정한다.
	- 기대하는 동작 방식은 Pig 클래스가 sound() 메서드를 오버라이딩 해서 "꿀꿀" 이라는 소리가 나도록 하는 것이다.
	- 그런데 개발자가 **실수로 sound() 메서드를 오버라이딩 하는 것을 빠트릴 수 있다.**
	- 이렇게 되면 부모의 기능을 상속 받기 때문에 **코드상 문제가 발생하지 않는다.**
	- 프로그램을 실행하면 "꿀꿀"이 아니라 **부모 클래스에 있는 animal.sound()가 호출된다.**


# 추상 클래스
>[!Question] 추상 클래스란?
>동물(Animal)과 같이 부모 클래스는 제공하지만, **실제 생성되면 안되는 클래스.**
>추상 클래스는 이름 그대로 **추상적인 개념을 제공**한다.
>따라서 **실체인 인스턴스가 존재하지 않는다.**
>**상속을 목적으로 사용**하고, **부모 클래스 역할을 담당**한다.

```java
public abstract class AbstractAnimal { ... }
```

- 추상 클래스는 클래스를 선언할 때 **abstract(추상) 키워드**를 붙여주면 된다.
- 추상 클래스는 **기존 클래스와 완전히 같지만**, `new AbstractAnimal()` 과 같이
  **직접 인스턴스를 생성하지 못하는 제약이 추가된 것**이다.

---


>[!Question] 추상 메서드란?
>부모 클래스를 상속 받는 **자식 클래스가 반드시 오버라이딩 해야하는 메서드.**
>추상 메서드는 **추상적인 개념을 제공**하는 메서드이다.
>따라서 **실체가 존재하지 않고, 메서드 바디가 없다.**

```java
public abstract void sound();
```
- 추상 메서드는 메서드를 선언할 때 **abstract(추상) 키워드**를 붙여주면 된다.
- **추상 메서드가 하나라도 있는 클래스는 추상 클래스로 선언해야 한다.**
	- 그렇지 않으면 컴파일 오류가 발생한다.
	- 추상 메서드는 메서드 바디가 없기 때문에 작도하지 않는 메서드를 가진 불완전한 클래스로 불 수 있으므로 추상 클래스로 선언해야 한다.
- **상속 받는 자식 클래스가 반드시 오버라이딩 해서 사용해야 한다.**
	- 그렇지 않으면 컴파일 오류가 발생한다.
	- 오버라이딩 하지 않으면 자식도 추상 클래스가 되어야 한다.
- 추상 메서드는 **기존 메서드와 완전히 같지만**, 메서드 바디가 없고, 자식 클래스가 해당 메서드를 **반드시 오버라이딩 해야 한다는 제약이 추가된 것**이다.

---
![[Pasted image 20241116123710.png]]
```java
package poly.ex3;  
  
public abstract class AbstractAnimal {  
  
    // 자식이 반드시 오버라이드 해야하는 목적  
    public abstract void sound();  
  
    // 자식이 상속 받아서 기능을 사용하는 목적  
    public void move() {  
        System.out.println("동물이 움직입니다.");  
    }  
}
```
- AbstractAnimal은 abstract가 붙은 추상 클래스
	- 이 클래스는 **직접 인스턴스를 생성할 수 없다.**
- sound()는 abstract가 붙은 추상 메서드
	- 이 메서드는 **자식이 반드시 오버라이딩 해야한다.**
- move()는 추상 메서드가 아니다.
	- 자식 클래스가 오버라이딩 하지 않아도 된다.

```java
// 추상클래스 생성 불가 -> 컴파일 오류 발생
// java: poly.ex3.AbstractAnimal is abstract; cannot be instantiated
AbstractAnimal abstractAnimal = new AbstractAnimal();
```


```java
package poly.ex3;  
  
public class Dog extends AbstractAnimal{  
// 추상 메서드는 반드시 오버라이딩 해야 한다.
//    @Override  
//    public void sound() {  
//        System.out.println("멍멍 !");//  
//    }  
}
```
```
Class 'Dog' must either be declared abstract or implement abstract method 'sound()' in 'AbstractAnimal'
```

>[!Success] 문제점 해결
>1. 추상 클래스 덕분에 **실수로 Animal 인스턴스를 생성할 문제**를 근본적으로 방지[[#^canCreateClass]]
>2. 추상 메서드 덕분에 새로운 동물의 자식 클래스를 만들 때 **실수로 sound()를 오버라이딩 하지 않을 문제**를 근본적으로 방지 [[#^possibilityOfNotOverriding]]


# 인터페이스
>[!Note] 순수 추상 클래스
>**모든 메서드가 추상 메서드인 순수 추상 클래스**는 코드를 실행할 바디 부분이 없다.
```java
package poly.ex4;  
  
public abstract class AbstractAnimal {  
  
    // 자식이 반드시 오버라이드 해야하는 목적  
    public abstract void sound();  
    public abstract void move();  
}
```


```java
package poly.ex4;  
  
public class Cat extends AbstractAnimal {  
    @Override  
    public void sound() {  
        System.out.println("야옹 ~!");  
    }  
  
    @Override  
    public void move() {  
        System.out.println("고양이 이동");  
    }  
}
```

- 순수 추상 클래스의 특징
	- **인스턴스를 생성할 수 없다.**
	- 상속 시 자식은 **모든 메서드를 오버라이딩 해야한다.**
	- 주로 다형성을 위해 사용된다.

>[!Note] 순수 추상클래스를 더 편리하게 사용할 수 있도록 **인터페이스**라는 개념을 제공한다.
```java
public interface InterfaceAnimal { 
	public abstract void sound(); 
	public abstract void move(); 
}
```

인터페이스: **public abstract 키워드 생략 가능**
```java
public interface InterfaceAnimal { 
	void sound(); 
	void move(); 
}
```
- **인터페이스는 순수 추상 클래스와 같고**, 약간의 편의 기능이 추가된다.
	- 인터페이스의 메서드는 모두 `public`, `abstrat`이다.
	- 메서드에 `public abstract`를 생략할 수 있다. **(권장)**
	- 인터페이스는 **다중 구현(다중 상속)을 지원한다.**


인터페이스와 멤버 변수
```java
public interface InterfaceAnimal { 
	public static final double MY_PI = 3.14; 
}
```
- 인터페이스에서 멤버 변수는 public, static, final이 모두 포함되었다고 간주한다.
	- static final: 정적이면서 고칠 수 없는 변수 = 상수
- 다음과 같이 생략할 수 있다. **(권장)**
```java
public interface InterfaceAnimal { 
	double MY_PI = 3.14; 
}
```
---
![[Pasted image 20241116133320.png]]
```java
package poly.ex5;  
  
public interface InterfaceAnimal {  
  
    void sound(); // public abstract  
    void move(); // public abstract  
}
```
```java
package poly.ex5;  
  
public class Cat implements InterfaceAnimal  
{  
    @Override  
    public void sound() {  
        System.out.println("야옹 ~!");  
    }  
  
    @Override  
    public void move() {  
        System.out.println("고양이 이동 === 333");  
    }  
}
```

- 인터페이스는 class 대신에 interface로 선언한다.
- sound(), move()는 public abstract가 생략되어 있다. (반드시 오버라이딩 해야함)
- 인터페이스를 상속 받을 때는 extends 대신에 implements(구현)라는 키워드 사용

```java
package poly.ex5;  
  
public class InterfaceMain {  
    public static void main(String[] args) {  
  
        // 인터페이스 생성 불가  
        // InterfaceAnimal interfaceAnimal = new InterfaceAnimal();  
  
        Cat cat = new Cat();  
        Dog dog = new Dog();  
        Caw caw = new Caw();  
  
        soundAnimal(cat);  
        soundAnimal(dog);  
        soundAnimal(caw);  
    }  
  
    public static void soundAnimal(InterfaceAnimal animal) {  
        System.out.println("동물 소리 테스트 시작");  
        animal.sound();  
        System.out.println("동물 소리 테스트 종료");  
    }  
}
```
![[Pasted image 20241116133616.png]]

>[!Note] 클래스, 추상 클래스, 인터페이스는 모두 똑같다.
>- 프로그램 코드, 메모리 구조상 모두 똑같다.
>- 자바에서는 모두 .class로 다루어진다.
>- 인터페이스는 메서드 이름만 있는 설계도이고, 이 설계도가 실제로 어떻게 작동하는지는 하위 클래스에서 모두 구현해야 한다. (상속은 부모의 기능을 물려 받는 것이 목적)
따라서 인터페이스의 경우 상속이 아니라 **인터페이스를 구현한다고 표현한다.**


# 인터페이스 다중 구현 ^whyUseInterface

>[!Note] 인터페이스를 사용해야하는 이유
>모든 메서드가 추상 메서드인 경우 순수 추상 클래스를 만들어도 되고, 인터페이스를 만들어도 된다. 그런데 왜 인터페이스를 사용해야 할까?
>1. 제약
>	- 인터페이스를 사용하는 이유는, 인터페이스를 구현하는 곳에서 메서드를 반드시 구현하라는 규약(제약)을 주는 것이다.
>	- **순수 추상 클래스의 경우, 미래에 누군가 실행 가능한 메서드를 끼워 넣을 수 있다.** 이렇게 되면 추가된 기능을 자식 클래스에서 구현하지 않을 수도 있고, **더는 순수 추상 클래스가 아니게 된다.**
>	- 인터페이스는 모든 메서드가 추상 메서드이기 때문에 **이런 문제를 원천 차단**할 수 있다.
>2. 다중 구현
>	- 자바에서 **클래스 상속은 부모를 하나만 지정**할 수 있다.
>	- 반면에 인터페이스는 부모를 여러명 두는 **다중 구현(다중 상속)이 가능**하다.



>[!Warning] 다중 상속의 문제점
그림과 같이 다중 상속을 사용하게 되면, `AirplaneCar` 입장에서 `move()`를 호출할 때
**어떤 부모의 move()를 사용해야 할지 애매한 문제가 발생한다.** = 다이아몬드 문제

![[Pasted image 20241116141733.png]]


>[!Success] 인터페이스의 다중 구현

![[Pasted image 20241116142009.png]]

- InterfaceA, InterfaceB는 둘다 같은 methodCommon()을 가지고 있다.
- 인터페이스 자신은 구현을 가지지 않고, **구현하는 곳에서 해당 기능을 모두 구현해야 한다.**
	- InterfaceA, InterfaceB는 같은 이름의 methodCommon()을 제공하지만,
	  이 기능은 **Child가 구현한다.**
	- 오버라이딩에 의해 **Child에 있는 methodCommon()이 호출된다.**
- 결과적으로 두 부모중에 어떤 한 부모의 methodCommon()을 선택하는 것이 아니라,
  **인터페이스들을 구현한 Child에 있는 methodCommon()이 사용된다.**
  이러한 이유로 인터페이스는 다이아몬드 문제가 발생하지 않는다.
  **따라서 인터페이스의 경우 다중 구현을 허용한다.**
```java
package poly.diamond;  
  
public interface InterfaceA {  
    void methodA();  
  
    void methodCommon();  
  
}
```

```java
package poly.diamond;  
  
public interface InterfaceB {  
    void methodB();  
  
    void methodCommon();  
  
}
```

```java
package poly.diamond;  
  
public class Child implements InterfaceA, InterfaceB {  
    @Override  
    public void methodA() {  
        System.out.println("Child.methodA");  
    }  
  
    @Override  
    public void methodB() {  
        System.out.println("Child.methodB");  
    }  
  
    @Override  
    public void methodCommon() {  
        System.out.println("Child.methodCommon");  
    }  
}
```
>[!Success] methodCommon()의 경우 양쪽 인터페이스에 다 있지만,같은 메서드이므로 구현은 하나만 하면 된다.



```java
package poly.diamond;  
  
public class DiamondMain {  
    public static void main(String[] args) {  
        InterfaceA interfaceA = new Child();  
        interfaceA.methodA();  
        interfaceA.methodCommon();  
  
  
        InterfaceB interfaceB = new Child();  
        interfaceB.methodB();  
        interfaceB.methodCommon();  
  
    }  
}
```

```
Child.methodA
Child.methodCommon
Child.methodB
Child.methodCommon
```
![[Pasted image 20241116142745.png]]
![[Pasted image 20241116142805.png]]