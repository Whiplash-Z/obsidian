
# 절차 지향 프로그래밍 - 시작
- 프로그래밍 방식
	- 절차 지향 프로그래밍
		- **실행 순서를 중요하게 생각하는 방식**
		- 프로그램의 **흐름을 순차적으로 따르며 처리하는 방식**
		- **"어떻게"를 중심**으로 프로그래밍 한다.
	- 객체 지향 프로그래밍
		- **객체를 중요하게 생각하는 방식**
		- 실제 세계의 사물이나 사건을 객체로 보고, 이러한 **객체들 간의 상호작용을 중심으로 프로그래밍하는 방식**
		- **"무엇을" 중심**으로 프로그래밍 한다.
- 차이점
	- 절차 지향은 **데이터와 해당 데이터에 대한 처리 방식이 분리**되어 있다.
	- 객체 지향은 **데이터와 그 데이터에 대한 행동(메서드)이 하나의 "객체"안에 포함**되어 있다.


```java
package oop;  
  
public class MusicPlayerMain1 {  
    public static void main(String[] args) {  
        int volume = 0;  
        boolean isOn = false;  
  
        // 음악 플레이어 켜기  
        isOn = true;  
        System.out.println("음악 플레이어를 시작합니다.");  
  
        //볼륨 증가  
        volume++;  
        System.out.println("음악 플레이어 볼륨 = " + volume);  
  
        //볼륨 증가  
        volume++;  
        System.out.println("음악 플레이어 볼륨 = " + volume);  
  
        //볼륨 감소  
        volume--;  
        System.out.println("음악 플레이어 볼륨 = " + volume);  
  
        // 음악 플레이어 상태  
        System.out.println("음악 플레이어 상태 확인");  
        if (isOn) {  
            System.out.println("음악 플레이어 ON, 볼륨: " + volume);  
        } else {  
            System.out.println("음악 플레이어 OFF");  
        }  
        // 음악 플레이어 끄기  
        isOn = false;  
        System.out.println("음악 플레이어를 종료합니다.");  
    }  
}
```



# 절차 지향 프로그래밍 - 데이터 묶음
```java
package oop;  
  
public class MusicPlayerData {  
    int volume = 0;  
    boolean isOn = false;  
}
```
```java
package oop;  
  
public class MusicPlayerMain2 {  
    public static void main(String[] args) {  
        // volume, isOn 속성을 MusicPlayerData의 멤버 변수에 포함.
        MusicPlayerData data = new MusicPlayerData();  
  
        // 음악 플레이어 켜기  
        data.isOn = true;  
        System.out.println("음악 플레이어를 시작합니다.");  
  
        //볼륨 증가  
        data.volume++;  
        System.out.println("음악 플레이어 볼륨 = " + data.volume);  
  
        //볼륨 증가  
        data.volume++;  
        System.out.println("음악 플레이어 볼륨 = " + data.volume);  
  
        //볼륨 감소  
        data.volume--;  
        System.out.println("음악 플레이어 볼륨 = " + data.volume);  
  
        // 음악 플레이어 상태  
        System.out.println("음악 플레이어 상태 확인");  
        if (data.isOn) {  
            System.out.println("음악 플레이어 ON, 볼륨: " + data.volume);  
        } else {  
            System.out.println("음악 플레이어 OFF");  
        }  
        // 음악 플레이어 끄기  
        data.isOn = false;  
        System.out.println("음악 플레이어를 종료합니다.");  
    }  
}
```
>[!success] 음악 플레이어의 관련된 데이터는 `MusicPlayerData`클래스에 존재한다.
>이 클래스를 사용하도록 로직을 변경하였기 때문에, 이후에 프로그램 로직이 더 복잡해져 다양한 변수들이 추가되더라도 음악 플레이어와 관련된 변수들은 `MusicPlayerData data` 객체에 속해있으므로 쉽게 구분할 수 있다.


# 절차 지향 프로그래밍 - 메서드 추출

```java
package oop;  
  
  
public class MusicPlayerMain3 {  
    public static void main(String[] args) {  
        MusicPlayerData data = new MusicPlayerData();  
  
        // 음악 플레이어 켜기  
        on(data);  
  
        //볼륨 증가  
        volumeUp(data);  
  
        //볼륨 증가  
        volumeUp(data);  
  
        //볼륨 감소  
        volumeDown(data);  
  
        // 음악 플레이어 상태  
        showStatus(data);  
  
        // 음악 플레이어 끄기  
        off(data);  
  
    }  
  
    static void on(MusicPlayerData data) {  
        data.isOn = true;  
        System.out.println("음악 플레이어를 시작합니다.");  
    }  
  
    static void off(MusicPlayerData data) {  
        data.isOn = false;  
        System.out.println("음악 플레이어를 종료합니다.");  
    }  
  
    static void volumeUp(MusicPlayerData data) {  
        data.volume++;  
        System.out.println("음악 플레이어 볼륨 = " + data.volume);  
    }  
  
    static void volumeDown(MusicPlayerData data) {  
        data.volume--;  
        System.out.println("음악 플레이어 볼륨 = " + data.volume);  
    }  
  
    static void showStatus(MusicPlayerData data) {  
        System.out.println("음악 플레이어 상태 확인");  
        if (data.isOn) {  
            System.out.println("음악 플레이어 ON, 볼륨: " + data.volume);  
        } else {  
            System.out.println("음악 플레이어 OFF");  
        }    }  
}
```

- 각각의 기능을 메서드로 만든 덕분에 기능이 **모듈화** 되었다.
	- **중복 제거**: 로직 중복 제거되었다. 같은 로직이 필요하면 해당 메서드를 여러번 호출하면 된다.
	- **변경 영향 범위**: 기능을 수정할 때 해당 메서드 내부만 변경하면 된다.
	- **메서드 이름 추가**: 메서드 이름을 통해 코드를 더 쉽게 이해할 수 있다.

>[!info] 모듈화
>쉽게 이야기 해서 레고 블럭을 생각하면 된다. 필요한 블럭을 가져다 꼽아서 사용할 수 있다.
>여기서는 음악 플레이어의 기능이 필요하면 해당 기능을 메서드 호출만으로 손쉽게 사용할 수 있다. 이제 음악 플레이어와 관련된 메서드를 조립해서 프로그램을 작성할 수 있다.

>[!warning] 절자 지향 프로그래밍의 한계
>- 클래스를 사용해서 관련된 데이터를 묶고, 메서드를 사용해서 각각의 기능을 모듈화해서 깔끔하고 읽기 좋고 유지보수 하기 좋은 코드를 작성할 수 있었다.
>- 하지만, **데이터와 기능이 분리되어 있다는 한계**에 부딪혔다.
>	- `MusicPlayerData`에 음악 플레이어의 데이터가 있는데, 이 데이터를 사용하는 기능은 `MusicPlayerMain3`에 있는 **각각의 메서드에 분리되어 있다.**
>	- 그래서 음악 플레이어와 관련된 데이터는 MusicPlayerData를 사용해야 하고, 음악 플레이어와 관련된 기능은 MusicPlayerMain3의 각 메서드를 사용해야 한다.
>	- 만약 관련 데이터가 변경되면 MusicPlayerMain3 부분의 메서드도 **함께 변경**해야 한다.
>	- **이렇게 데이터와 기능이 분리되어 있으면 유지보수 관점에서도 관리 포인트가 2곳으로 늘어난다.**
>- ==**하지만, 객체 지향 프로그래밍이 나오면서 데이터와 기능을 온전히 하나로 묶어서 사용할 수 있게 되었다.**==



# 클래스와 메서드
- 클래스는 데이터인 멤버 변수 뿐 아니라 기능 역할을 하는 메서드도 포함할 수 있다.

```java
package oop;  
  
public class ValueObject {  
    int value;  
  
    void add() {  
        value++;  
        System.out.println("숫자 증가 value = " + value);  
    }  
}
```
```java
package oop;  
  
public class ValueObjectMain {  
    public static void main(String[] args) {  
        ValueObject valueObject = new ValueObject();  
        valueObject.add();  
        valueObject.add();  
        valueObject.add();  
        System.out.println("최종 숫자 = " + valueObject.value);  
    }  
}
```

`ValueObject valueObject = new ValueObject();`
![[Pasted image 20241113142416.png]]
- 이 객체는 메버 변수 뿐만 아니라 내부에 기능을 수행하는 `add()`메서드도 **함께 존재한다.**

![[Pasted image 20241113142515.png]]

- 클래스는 속성(데이터, 멤버 변수)과 기능(메서드)을 정의할 수 있다.
- 객체는 자신의 메서드를 통해 자신의 멤버 변수에 접근할 수 있다.
	- 객체의 메서드 내부에서 접근하는 멤버 변수는 **객체 자신의 멤버 변수이다.**


# 객체 지향 프로그래밍
```java
package oop;  
  
public class MusicPlayer {  
  
    int volume = 0;  
    boolean isOn = false;  
  
    void on() {  
        isOn = true;  
        System.out.println("음악 플레이어를 시작합니다.");  
    }  
  
    void off() {  
        isOn = false;  
        System.out.println("음악 플레이어를 종료합니다.");  
    }  
  
    void volumeUp() {  
        volume++;  
        System.out.println("음악 플레이어 볼륨 = " + volume);  
    }  
  
    void volumeDown() {  
        volume--;  
        System.out.println("음악 플레이어 볼륨 = " + volume);  
    }  
  
    void showStatus() {  
        System.out.println("음악 플레이어 상태 확인");  
        if (isOn) {  
            System.out.println("음악 플레이어 ON, 볼륨: " + volume);  
        } else {  
            System.out.println("음악 플레이어 OFF");  
        }    }  
}
```
- `MusicPlayer` **클래스에** 음악 플레이어에 필요한 **속성과 기능을 모두 정의했다.**
- 이 클래스만 있으면 온전한 음악 플레이어를 생성해서 사용할 수 있다.
 **==-> 음악 플레이어를 사용하는데 필요한 모든 속성과 기능이 하나의 클래스에 포함되어 있다!==**

```java
package oop;  
  
public class MusicPlayerMain4 {  
    public static void main(String[] args) {  
        MusicPlayer player = new MusicPlayer();  
  
        // 음악 플레이어 켜기  
        player.on();  
        //볼륨 증가  
        player.volumeUp();  
  
        //볼륨 증가  
        player.volumeUp();  
        //볼륨 감소  
        player.volumeDown();  
  
        // 음악 플레이어 상태  
        player.showStatus();  
  
        // 음악 플레이어 끄기  
        player.off();  
    }  
}
```
- `MusicPlayer`객체를 생성하고 필요한 기능(메서드)을 호출하기만 하면 된다.
- 필요한 모든 것은 `MusicPlayer`안에 들어있다.
- `MusicPlayer`를 사용하는 입장에서는 `MusicPlayer` 내부에 어떤 속성(데이터)이 있는지 전혀 몰라도 된다.
- `MusicPlayer`를 사용하는 입장에서는 단순하게 `MusicPlayer`가 제공하는 기능 중에 필요한 기능을 호출해서 사용하기만 하면 된다.

- **속성과 기능이 한 곳에 있기 때문에 변경도 더 쉬워진다.**
	- `MusicPlayer`내부 코드가 변하는 경우에 다른 코드는 변경하지 않아도 된다.
	- `MusicPlayer`의 volume이라는 필드 이름이 다른 이름으로 변한다고 할 때 `MusicPlayer`내부만 변경하면 된다.
	- `MusicPlayer` 내부에서 출력하는 메시지를 변경할 때도 `MusicPlayer` 내부만 변경하면 된다.

>[!info] 캡슐화
>`MusicPlayer`를 보면 음악 플레이어를 구성하기 위한 속성과 기능이 마치 하나의 캡슐에 쌓여 있는 것 같다. 이렇게 **속성과 기능을 하나로 묶어서 필요한 기능을 메서드를 통해 외부에 제공하는 것을 캡슐화라고 한다.**



객체지향 프로그래밍은 속성과 기능이 하나의 클래스에 묶여서 캡슐화가 되어있는 것이다.