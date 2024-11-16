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





# 클래스와 인터페이스 활용