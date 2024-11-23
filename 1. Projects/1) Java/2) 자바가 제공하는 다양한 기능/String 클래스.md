## String 비교
>[!Note] String 클래스를 비교할 때는 == 비교가 아니라 항상 equals() 비교를 해야한다.
>- 동일성(Identity): == 연산자로 두 객체의 참조가 동일한 객체를 가르키는지 확인
>- 동등성(Equality): equals() 메서드를 사용하여 두 객체가 논리적으로 같은지 확인

```java
public class StringEqualsMain1 {  
  
    public static void main(String[] args) {  
  
        String str1 = new String("hello");  
        String str2 = new String("hello");  
        System.out.println("메서드 호출 비교1: " + isSame(str1, str2));  
  
        String str3 = "hello";  
        String str4 = "hello";  
  
        System.out.println("메서드 호출 비교2: " + isSame(str3, str4));  
  
    }  
  
    private static boolean isSame(String x, String y) {  
        // return x == y;  
        return x.equals(y);  
    }  
}
```
```
메서드 호출 비교1: true
메서드 호출 비교2: true

```
![[Pasted image 20241123235204.png]]
- str1과 str2는 new String()을 사용해서 각각 인스턴스를 생성했기 때문에 서로 다른 인스턴스이므로 동일성(\=\=) 비교에 실패한다.
- 둘은 내부에 같은 "hello" 값을 가지고 있기 때문에 논리적으로 같으므로 동등성(equals) 비교에 성공한다.
- String 클래스는 내부 문자열 값을 비교하도록 equals() 메서드를 재정의 해두었다.
- String str3 = "hello"와 같이 문자열 리터럴을 사용하는 경우 자바는 메모리 효율성과 성능 최적화를 위해 **문자열 풀을 사용한다.**
	- 자바가 실행되는 시점에 클래스에 문자열 리터럴이 있으면 문자열 풀에 String 인스턴스를 미리 만들어둔다.
	- String str3 = "hello" 와 같이 문자열 리터럴을 사용하면 문자열 풀에서 "hello" 라는 문자를 가진 String 인스턴스를 찾는다. 그리고 찾은 인스턴스의 참조( x003 )를 반환한다.
	- String str4 = "hello" 의 경우 "hello" 문자열 리터럴을 사용하므로 문자열 풀에서 str3 과 같은 x003 참조를 사용한다.
	- 문자열 풀 덕분에 같은 문자를 사용하는 경우 메모리 사용을 줄이고 문자를 만드는 시간도 줄어들기 때문에 성능도 최적화 할 수 있다.
- 항상 equals() 비교를 해야하는 이유
	- main() 메서드를 만드는 개발자와 isSame() 메서드를 만드는 개발자가 서로 다르다고 가정해보자.
	- isSame() 의 경우 매개변수로 넘어오는 String 인스턴스가 new String() 으로 만들어진 것인지, 문자열 리터럴로 만들어 진 것인지 확인할 수 있는 방법이 없다.
	- 따라서 문자열 비교는 항상 equals() 를 사용해서 동등성 비교를 해야 한다.


## String은 불변 객체
>[!Note] String은 불변 객체이다. 따라서 생성 이후에 절대로 내부의 문자열 값을 변경할 수 없다.

![[Pasted image 20241124000032.png]]
```java
public class StringImmutable2 {  
    public static void main(String[] args) {  
        String str1 = "hello";  
        String str2 = str1.concat(" java");  
        System.out.println("str1 = " + str1);  
        System.out.println("str = " + str2);  
    }  
}
```
```
str1 = hello
str = hello java
```
-> String은 불변객체이므로 변경이 필요한 경우 기존 값을 변경하지 않고, 새로운 결과를 만들어서 반환한다.

## StringBuilder 가변 String
>[!Warning] 불변인 String 클래스의 단점
>- 문자를 더하거나 변경할 때 마다 **계속해서 새로운 객체를 생성해야 한다.**
>- 문자를 자주 더하거나 변경해야 하는 상황이라면 더 많은 String 객체를 만들고,
>  GC해야 한다.
>- 결과적으로 컴퓨터의 CPU, 메모리를 자원을 더 많이 사용하게 된다.
>- 문자열의 크기가 클수록, 문자열을 더 자주 변경할수록 시스템의 자원을
>  더 많이 소모한다.

>[!Success] StringBuilder는 가변적이다.
>- 하나의 StringBuilder 객체 안에서 문자열을 추가, 삭제, 수정할 수 있고,
>  이때마다 새로운 객체를 생성하지 않는다.
>- 이로인해 메모리 사용을 줄이고 성능을 향상시킬 수 있다.
>- 단, 사이드 이펙트를 주의해야 한다.
>- StringBuilder는 보통 문자열을 변경하는 동안만 사용하다가 문자열 변경이 끝나면 안전한(불변) String으로 변경하는 것이 좋다.

```java
public class LoopStringBuilderMain {  
  
    public static void main(String[] args) {  
        long startTime = System.currentTimeMillis();  
  
        StringBuilder sb = new StringBuilder();  
        for (int i = 0; i < 100000; i++) {  
            sb.append("Hello Java");  
        }        long endTime = System.currentTimeMillis();  
  
        String result = sb.toString();  
        System.out.println("result = " + result);  
        System.out.println("time = " + (endTime - startTime) + "ms");  
    }  
}
```



