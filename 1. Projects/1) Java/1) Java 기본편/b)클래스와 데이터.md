# 클래스가 필요한 이유

## 학생 데이터 관리
```java
package class1;  
  
public class ClassStart1 {  
    public static void main(String[] args) {  
        String student1Name = "학생 1";  
        int student1Age = 15;  
        int student1Grage = 90;  
  
        String student2Name = "학생 2";  
        int student2Age = 16;  
        int student2Grage = 80;  
  
        System.out.println("이름: " + student1Name + " 나이: " + student1Age + " 성적: " + student1Grage);  
        System.out.println("이름: " + student2Name + " 나이: " + student2Age + " 성적: " + student2Grage);  
    }  
}
```

- 이 코드의 문제점
	- 학생이 늘어날 때마다 **변수를 추가로 선언**해야 하고, **출력하는 코드도 추가**해야 한다.
- 해결 방법
	- **배열을 사용해서 문제를 해결**할 수 있다.

## 배열을 사용해서 코드 변경을 최소화
```java
package class1;  
  
public class ClassStart2 {  
    public static void main(String[] args) {  
        String[] studentNames = {"학생1", "학생3", "학생4", "학생5"};
		int[] studentAges = {15, 17, 10, 16};
		int[] studentGrades = {90, 100, 80, 50};  
  
        for (int i = 0; i < studentNames.length; i++) {  
            System.out.println("이름: " + studentNames[i] + " 나이: " + studentAges[i] + " 성적: " + studentGrade[i]);  
        }  
    }  
}
```
- 배열을 사용한 덕분에 학생이 추가되어도 배열에 학생의 데이터만 추가하면 된다.
	- 이제 **변수를 더 추가하지 않아도 되고, 출력 부분의 코드도 그대로 유지**할 수 있다.
- 배열 사용의 한계
	- 배열을 사용해서 코드 변경을 최소화하는데는 성공했지만, 한 학생의 데이터가 `studentNames[]`, `studentAges[]`, `studentGrade[]` 라는 **3개의 배열에 나누어져 있다.**
	- 따라서, **데이터를 변경할 때 매우 조심해서 작업해야 한다.**
		- 학생 2의 데이터를 제거하려면 각각의 배열마다 학생 2의 요소를 **정확하게 찾아서 제거해줘야 한다.**
		- 특정 학생의 **데이터를 변경할 때 실수할 가능성이 매우 높다.**
>[!success] 
>지금처럼 이름, 나이, 성적을 각각 따로 나눠서 관리하는 것은 **사람이 관리하기 좋은 방식이 아니다.**
>사람이 관리하기 좋은 방식은 각각의 학생 별로 이름, 나이, 성적을 관리할 수 있도록 **학생이라는 개념을 하나로 묶는 것이다.**
>



# 클래스 도입
```java
package class1;  
  
public class Student {  
    String name;  
    int age;  
    int grade;  
}
```
- `class` 키워드를 사용해서 학생 클래스를 정의한다.
	- 학생 클래스는 내부에 이름, 나이, 성적 변수를 가진다.
	- **클래스에 정의한 변수**들을 **멤버 변수** 또는 **필드**라고 한다.
		- 멤버 변수(Member Variable): 특정 클래스에 소속된 멤버라는 의미
		- 필드(Field): 데이터 항목을 가르키는 용어.
		- 자바에서는 멤버 변수, 필드는 같은 뜻으로 **클래스에 소속된 변수를 뜻한다.**

## 클래스와 사용자 정의 타입
```java
package class1;  
  
public class ClassStart3 {  
    public static void main(String[] args) {  
        Student student1;  
        student1 = new Student();  
        student1.name = "학생 1";  
        student1.age = 15;  
        student1.grade = 90;  
  
        Student student2 = new Student();  
        student2.name = "학생 2";  
        student2.age = 16;  
        student2.grade = 80;  
  
        System.out.println("이름: " + student1.name + " 나이: " + student1.age + " 성적: " + student1.grade);  
        System.out.println("이름: " + student2.name + " 나이: " + student2.age + " 성적: " + student2.grade);  
    }  
}
```
- 타입은 데이터의 종류나 형태를 나타낸다.
- **클래스를 사용하면 타입을 직접 만들 수 있다.** (Student 타입)
	- 사용자가 직접 정의하는 사용자 정의 타입을 만들려면 설계도가 필요하다.
	- **이 설계도가 바로 클래스이다.**
- 설계도인 **클래스를 사용해서 실제 메모리에 만들어진 실체**를 **객체** 또는 **인스턴스**라고 한다.

## 객체에 접근할 수 있는 방법
![[Pasted image 20241113104241.png]]

- `new` 키워드를 통해 객체를 생성하면 자바는 메모리에 이 **객체에 접근할 수 있는 참조값(주소)(x001)을 반환한다.**
- `Student student` 변수에 객체의 참조값을 보관하면, **이 변수는 메모리에 존재하는 실제 인스턴스의 참조값을 가지고 있다.**
- 이 변수를 통해서 **객체를 접근(참조)해서 사용할 수 있다.**
