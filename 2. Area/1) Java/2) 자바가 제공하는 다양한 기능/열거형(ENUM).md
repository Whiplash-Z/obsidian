# 문자열을 사용하는 방식의 문제점

```java
public class DiscountService {  
  
    public int discount(String grade, int price) {  
        int discountPercent = 0;  
  
        if (grade.equals("BASIC")) {  
            discountPercent = 10;  
        } else if (grade.equals("GOLD")) {  
            discountPercent = 20;  
        } else if (grade.equals("DIAMOND")) {  
            discountPercent = 30;  
        } else {  
            System.out.println(grade + ": 할인 X");  
        }  
        return price * discountPercent / 100;  
    }  
}
```
```java
public class StringGradeEx0_2 {  
    public static void main(String[] args) {  
        int price = 10000;  
  
        DiscountService discountService = new DiscountService();  
  
        // 존재하지 않는 등급 (BASIC, GOLD, DIAMOND만 존재하는 상황)
        int vip = discountService.discount("VIP", price);  
        System.out.println("VIP 등급의 할인 금액: " + vip);  
  
        // 오타  
        int diamondd = discountService.discount("DIAMONDD", price);  
        System.out.println("DIAMONDD 등급의 할인 금액: " + diamondd);  
  
        // 소문자 입력  
        int gold = discountService.discount("gold", price);  
        System.out.println("gold 등급의 할인 금액: " + gold);  
  
    }  
}
```

>[!Warning] 문자열을 사용하는 방식의 문제점
>- 타입 안정성 부족: 문자열은 오타가 입력 되었을 때 인지하기 어렵고, 유효하지 않은 값이 입력될 수 있다. **(컴파일 시 오류 감지 불가)**
>- 데이터 일관성: "GOLD", "gold", "Gold" 등 다양한 형식으로 문자열을 입력할 수 있어서 일관성이 떨어진다.


## 상수를 사용하자
```java
public class StringGrade {  
    public static final String BASIC = "BASIC";  
    public static final String GOLD = "GOLD";  
    public static final String DIAMON = "DIAMON";  
}
```
```java
public class DiscountService {  
  
    public int discount(String grade, int price) {  
        int discountPercent = 0;  
  
        if (grade.equals(StringGrade.BASIC)) {  
            discountPercent = 10;  
        } else if (grade.equals(StringGrade.GOLD)) {  
            discountPercent = 20;  
        } else if (grade.equals(StringGrade.DIAMON)) {  
            discountPercent = 30;  
        } else {  
            System.out.println(grade + ": 할인 X");  
        }  
        return price * discountPercent / 100;  
    }  
}
```
```java
public class StringGradeEx0_1 {  
    public static void main(String[] args) {  
        int price = 10000;  
  
        DiscountService discountService = new DiscountService();  
        int basic = discountService.discount(StringGrade.BASIC, price);  
        int gold = discountService.discount(StringGrade.GOLD, price);  
        int diamond = discountService.discount(StringGrade.DIAMON, price);  
  
        System.out.println("BASIC 등급의 할인 금액: " + basic);  
        System.out.println("GOLD 등급의 할인 금액: " + gold);  
        System.out.println("DIAMOND 등급의 할인 금액: " + diamond);  
    }  
}
```
- 문자열 상수를 사용한 덕분에 전체적으로 코드가 더 명확해졌다.
	- 실수로 상수의 이름을 잘못 입력하면 컴파일 시점에 오류가 발생한다.
- 하지만, 문제를 근본적으로 해결한 것이 아니다.
	- 파라미터는 String 값을 전달받도록 설계되어 있기 때문에 상수로 선언한 StringGrade를 사용하지 않고 문자열을 전달해도 컴파일 시점에 문제를 파악하기 어렵다.

<이전처럼 문자열을 입력해도 컴파일러가 문제를 잡아내지 못함>
```java
public class StringGradeEx0_2 {  
    public static void main(String[] args) {  
        int price = 10000;  
  
        DiscountService discountService = new DiscountService();  
  
        // 존재하지 않는 등급  
        int vip = discountService.discount("VIP", price);  
        System.out.println("VIP 등급의 할인 금액: " + vip);  
  
        // 오타  
        int diamondd = discountService.discount("DIAMONDO", price);  
        System.out.println("DIAMONDD 등급의 할인 금액: " + diamondd);  
  
        // 소문자 입력  
        int gold = discountService.discount("gold", price);  
        System.out.println("gold 등급의 할인 금액: " + gold);  
  
    }  
}
```

## 타입 안전 열거형 패턴 도입

^7552df

```java
public class ClassGrade {  
    public static final ClassGrade BASIC = new ClassGrade();  
    public static final ClassGrade GOLD = new ClassGrade();  
    public static final ClassGrade DIAMOND = new ClassGrade();  
  
    // privatge 생성자 추가 (외부에서 생성 불가)
    private ClassGrade() {}  
}
```
```java
public class DiscountService {  
  
    public int discount(ClassGrade classGrade, int price) {  
        int discountPercent = 0;  
  
        if (classGrade == ClassGrade.BASIC) {  
            discountPercent = 10;  
        } else if (classGrade == ClassGrade.GOLD) {  
            discountPercent = 20;  
        } else if (classGrade == ClassGrade.DIAMOND) {  
            discountPercent = 30;  
        } else System.out.println("할인 X");  
  
        return price * discountPercent / 100;  
    }  
}
```

```java
public class ClassGradeEx2_1 {  
    public static void main(String[] args) {  
        int price = 10000;  
  
  
        DiscountService discountService = new DiscountService();  
    
		// int error = discountService.discount("error"); -> 컴파일 에러
      
        int basic = discountService.discount(ClassGrade.BASIC, price);  
        int gold = discountService.discount(ClassGrade.GOLD, price);  
        int diamond = discountService.discount(ClassGrade.DIAMOND, price);  
  
        System.out.println("BASIC 등급의 할인 금액: " + basic);  
        System.out.println("GOLD 등급의 할인 금액: " + gold);  
        System.out.println("DIAMOND 등급의 할인 금액: " + diamond);  
  
    }  
}
```
- 타입 안전 열거형 패턴의 장점
	- 타입 안정성 향상
		- 정해진 객체만 사용할 수 있기 때문에 잘못된 값을 입력하는 문제를 근본적으로 막을 수 있다. (컴파일 시점에 문제 파악 가능)
	- 데이터 일관성
		- 정해진 객체만 사용하므로 데이터의 일관성이 보장된다.

## ENUM Type
```java
public enum Grade {  
    BASIC, GOLD, DIAMOND  
}
```
위의 코드와 아래의 코드는 같다.
```java
public class ClassGrade {  
    public static final ClassGrade BASIC = new ClassGrade();  
    public static final ClassGrade GOLD = new ClassGrade();  
    public static final ClassGrade DIAMOND = new ClassGrade();  
  
    // privatge 생성자 추가  
    private ClassGrade() {}  
}
```

<검증>
```java
public class EnumRefMain {  
    public static void main(String[] args) {  
        System.out.println("class BASIC = " + Grade.BASIC.getClass());  
        System.out.println("class GOLD = " + Grade.GOLD.getClass());  
        System.out.println("class DIAMOND = " + Grade.DIAMOND.getClass());  
  
        System.out.println();  
  
        System.out.println("ref BASIC = " + refValue(Grade.BASIC));  
        System.out.println("ref GOLD = " + refValue(Grade.GOLD));  
        System.out.println("ref DIAMOND = " + refValue(Grade.DIAMOND));  
    }  
  
    private static String refValue(Object grade) {  
        return Integer.toHexString(System.identityHashCode(grade));  
    }  
}
```
```
class BASIC = class ennumeration.ex3.Grade
class GOLD = class ennumeration.ex3.Grade
class DIAMOND = class ennumeration.ex3.Grade

ref BASIC = 30f39991
ref GOLD = 452b3a41
ref DIAMOND = 4a574795
```


<사용>
```java
public class DiscountService {  
  
    public int discount(Grade classGrade, int price) {  
        int discountPercent = 0;  
  
        if (classGrade == Grade.BASIC) { // 30f39991 객체 비교
            discountPercent = 10;  
        } else if (classGrade == Grade.GOLD) {  // 452b3a41 객체 비교
            discountPercent = 20;  
        } else if (classGrade == Grade.DIAMOND) {  // 4a574795 객체 비교
            discountPercent = 30;  
        } else System.out.println("할인 X");  
  
        return price * discountPercent / 100;  
    }  
}
```
```java
public class ClassGradeEx3_1 {  
    public static void main(String[] args) {  
        int price = 10000;  
  
  
        DiscountService discountService = new DiscountService();  
  
        // 컴파일 에러  
        // new Grade(); -> Enum types cannot be instantiated   
        // discountService.discount("error", price); -> Type Error  
  
        int basic = discountService.discount(BASIC, price);  
        int gold = discountService.discount(GOLD, price);  
        int diamond = discountService.discount(DIAMOND, price);  
  
        System.out.println("BASIC 등급의 할인 금액: " + basic);  
        System.out.println("GOLD 등급의 할인 금액: " + gold);  
        System.out.println("DIAMOND 등급의 할인 금액: " + diamond);  
  
    }  
}
```

## ENUM(열거형) 주요 메서드
```java
public class EnumMethodMain {  
  
    public static void main(String[] args) {  
  
        // 모든 ENUM 반환  
        Grade[] values = Grade.values();  
        System.out.println("values = " + Arrays.toString(values));  
        for (Grade value : values) {  
            System.out.println("name = " + value.name() + ", ordinal= " + value.ordinal());  
        }  
  
        // String -> ENUM 변환  
        String input = "GOLD";  
        Grade gold = Grade.valueOf(input);  
        System.out.println("gold = " + gold);  
  
    }  
}
```
```
values = [BASIC, GOLD, DIAMOND]
name = BASIC, ordinal= 0
name = GOLD, ordinal= 1
name = DIAMOND, ordinal= 2
gold = GOLD
```


# 열거형 리팩터링
```java
public enum Grade {  
    BASIC(10),  
    GOLD(20),  
    DIAMOND(30);  
  
    private final int discountPercent;  
  
    Grade(int discountPercent) {  
        this.discountPercent = discountPercent;  
    }  
  
    // 추가  
    public int discount(int price) {  
        return price * discountPercent / 100;  
    }  
}
```

```java
public class ClassGradeRefMain3_4 {  
    public static void main(String[] args) {  
        int price = 10000;  
  
        Grade[] grades = Grade.values();  
        for (Grade grade : grades) {  
            printDiscount(grade, price);  
        }    }  
  
    private static void printDiscount(Grade grade, int price) {  
        System.out.println(grade.name() + " 등급의 할인 금액: " + grade.discount(price));  
    }  
}
```