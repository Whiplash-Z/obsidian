# 문자열 해시 코드
>[!Warning] 문자 데이터를 기반으로 숫자 해시 인덱스를 구하려면 어떻게 해야할까?
>지금까지 해시 인덱스를 구할 때 **숫자를 기반으로 해시 인덱스를 구했다.**
>해시 인덱스는 배열의 인덱스로 사용해야 하므로 0,1,2 같은 **숫자(양의 정수)만 사용**할 수 있다. 따라서 **문자를 구할 수 없다.**

>[!info] 모든 문자가 고유한 숫자를 가지고 있다. (ASCII CODE)

![[Pasted image 20250528111101.png]]

# 해시 코드와 해시 인덱스
![[Pasted image 20250528112930.png]]

```java
package set;  
  
public class StringHashMain {  
  
    static final int CAPACITY = 10;  
  
    public static void main(String[] args) {  
        // char  
        char charA = 'A';  
        char charB = 'B';  
        System.out.println("charA = " + (int) charA);  
        System.out.println("charB = " + (int) charB);  
  
        // hashCode  
        System.out.println("hashCode('A') = " + hashCode("A"));  
        System.out.println("hashCode('B') = " + hashCode("B"));  
        System.out.println("hashCode('AB') = " + hashCode("AB"));  
  
        // hashIndex  
        int hashCodeA = hashCode("A");  
        int hashIndex = hashIndex(hashCodeA);  
        System.out.println("hashIndex = " + hashIndex);  
  
        System.out.println("hashIndex(A) = " + hashIndex(hashCode("A")));  
        System.out.println("hashIndex(B) = " + hashIndex(hashCode("B")));  
        System.out.println("hashIndex(AB) = " + hashIndex(hashCode("AB")));  
    }  
  
    // 문자를 넣으면 숫자를 반환  
    static int hashCode(String string) {  
        char[] charArray = string.toCharArray();  
        int sum = 0;  
        for (char c : charArray) {  
            sum += c; // sum = sum + (int) c;  
        }  
  
        return sum;  
    }  
  
    static int hashIndex(int value) {  
        return value % CAPACITY;  
    }  
  
  
}
```
```
charA = 65
charB = 66
```

# 용어 정리
- 해시 함수 (Hash Function)
	- 해시 함수는 임의의 길이의 데이터를 입력으로 받아, 고정된 길이의 **해시값(해시 코드)을 출력하는 함수**이다.
		- 고정된 길이 = 저장 공간의 크기
		- ex) int형 1, 100 => 4byte를 차지하는 고정된 길이
- 같은 데이터를 입력하면 항상 같은 해시 코드가 출력된다.
- 다른 데이터를 입력해도 같은 해시 코드가 출력될 수 있다.
	- "BC" -> 66 + 67 => 133
	- "AD" -> 65 + 68 = 133
	- => 해시 충돌
- 해시 코드 (Hash Code)
	- **데이터를 대표하는 값**을 뜻한다.
	- 데이터 A의 해시 코드는 65
	- 데이터 B의 해시 코드는 66
	- 데이터 AB의 해시 코드는 131
- 해시 인덱스 (Hash Index)
	- **데이터의 저장 위치**를 결정하는 값.
	- 주로 해시 코드를 사용해서 만든다.
	- 보통 해시 코드의 결과에 배열의 크기를 나누어 구한다.

# 정리
- 문자 데이터를 사용할 때도, 해시 함수를 사용해서 정수 기반의 해시 코드로 변환한 덕분에 해시 인덱스를 사용할 수 있게 되었다.
- 따라서 문자의 경우에도 해시 인덱스를 통해 빠르게 저장하고 조회할 수 있다.

>[!Question] 내가 직접 만든 Member, User와 같은 객체는 어떻게 해시 코드를 정의할 수 있을까?

- 여기서 핵심은 **해시 코드**
- 세상의 어떤 객체든지 정수로 만든 해시 코드만 정의할 수 있다면 해시 인덱스를 사용할 수 있다.
- 자바의 `hashCode()` 메서드에 대해 알아보자.

