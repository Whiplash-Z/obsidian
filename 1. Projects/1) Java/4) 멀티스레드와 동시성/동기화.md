# synchronized
>[!Warning] 멀티스레드를 사용할 때 가장 주의해야할 점
>- 같은 자원(리소스)에 여러 스레드가 동시에 접근할 때 발생하는 동시성 문제
>- 여러 스레드가 접근하는 자원을 공유 자원이라 한다.
>- 대표적인 공유 자원은 인스턴스의 필드(멤버 변수)이다.

```java
public class BankAccountV1 implements BankAccount {  
  
    private int balance;  
  
    public BankAccountV1(int initialBalance) {  
        this.balance = initialBalance;  
    }  
  
    @Override  
    public boolean withdraw(int amount) {  
        log("거래 시작: " + getClass().getSimpleName());  
        // 잔고가 출금액 보다 적으면, 진행하면 안됨  
  
        log("[검증 시작] 출금액: " + amount + ", 잔액: " + balance);  
        if (balance < amount) {  
            log("[검증 실패] 출금액: " + amount + ", 잔액: " + balance);  
            return false;  
        }  
        // 잔고가 출금액 보다 많으면, 진행  
        log("[검증 완료] 출금액: " + amount + ", 잔액: " + balance);  
        sleep(1000); // 출금에 걸리는 시간으로 가정  
        balance = balance - amount;  
  
        log("[출금 완료] 출금액: " + amount + ", 잔액: " + balance);  
  
        log("거래 종료");  
        return true;  
    }  
  
    @Override  
    public int getBalance() {  
        return balance;  
    }  
}
```
```java
public class BankMain {  
    public static void main(String[] args) throws InterruptedException {  
        BankAccountV1 account = new BankAccountV1(1000);  
  
        Thread t1 = new Thread(new WithdrawTask(account, 800), "t1");  
        Thread t2 = new Thread(new WithdrawTask(account, 800), "t2");  
        t1.start();  
        t2.start();  
  
        sleep(500); // 검증 완료까지 잠시 대기  
        log("t1 state: " + t1.getState());  
        log("t2 state: " + t2.getState());  
  
        t1.join();  
        t2.join();  
  
        log("최종 잔액: " + account.getBalance());  
    }  
}
```
```
21:28:40.641 [       t1] 거래 시작: BankAccountV1
21:28:40.641 [       t2] 거래 시작: BankAccountV1
21:28:40.648 [       t1] [검증 시작] 출금액: 800, 잔액: 1000
21:28:40.648 [       t2] [검증 시작] 출금액: 800, 잔액: 1000
21:28:40.649 [       t1] [검증 완료] 출금액: 800, 잔액: 1000
21:28:40.649 [       t2] [검증 완료] 출금액: 800, 잔액: 1000
21:28:41.130 [     main] t1 state: TIMED_WAITING
21:28:41.130 [     main] t2 state: TIMED_WAITING
21:28:41.655 [       t1] [출금 완료] 출금액: 800, 잔액: 200
21:28:41.655 [       t1] 거래 종료
21:28:41.655 [       t2] [출금 완료] 출금액: 800, 잔액: -600
21:28:41.657 [       t2] 거래 종료
21:28:41.658 [     main] 최종 잔액: -600
```
![[Pasted image 20241206213352.png]]
- t1 스레드의 run()에서 withdraw()를 실행한다.
- 거의 동시에 t2 스레드의 run()에서 withdraw()를 실행한다.
- t1 스레드와 t2 스레드는 같은 BankAccount(x001) 인스턴스의 withdraw() 메서드를 호출한다.
- 따라서 두 스레드는 같은 BankAccount(x001) 인스턴스에 접근하고 또 x001 인스턴스에 있는 잔액(balance) 필드도 함께 사용한다.

> 예상 시나리오
- t1 스레드가 800원을 출금하면 잔액 200원이 남는다.
- t2 스레드가 800원을 출금하면 잔액보다 더 많은 돈을 출금하게 되므로 출금에 실패한다.

> 실제 실행 흐름
- 기대와는 다르게 t1, t2는 각각 800원씩 총 1600원 출금에 성공한다.
- 계좌의 잔액은 -600원이 되어있고, **계좌는 예상치 못하게 마이너스 금액이 되었다.**

>[!Question] 분명히 잔고를 체크하는 로직이 있는데, 왜 이런 문제가 발생했을까?


# 동시성 문제
![[Pasted image 20241206213901.png]]

![[Pasted image 20241206214015.png]]
- 바로 이 부분이 문제다 !
- **아직 잔액(balance)을 줄이지 못했기 때문에** t2는 검증 로직에서 현재 잔액을 1000원으로 확인한다.

![[Pasted image 20241206214348.png]]
- t1은 800원을 출금하면서, 잔액을 1000원에서 800원 만큼 차감한다.
- 이제 계좌의 잔액은 200원이 된다.

![[Pasted image 20241206214419.png]]
- t2는 800원을 출금하면서, 잔액을 200원에서 800원 만큼 차감한다.
- 이제 잔액은 -600원이 된다.

![[Pasted image 20241206214447.png]]

# 임계 영역
: **여러 스레드가 동시에 접근**하면 **데이터 불일치**나 **예상치 못한 동작이 발생**할 수 있는 위험하고 또 중요한 코드 부분

>[!Info] 이런 문제가 발생한 근본 원인은 여러 스레드가 함께 사용하는 공유 자원을 여러 단계로 나누어 사용하기 때문이다.
1. 검증 단계: 잔액(balance)이 출금액(amount) 보다 많은지 확인한다.
2. 출금 단계: 잔액(balance)이 출금액(amount) 만큼 줄인다.

- 이 로직에는 하나의 큰 가정이 있다.
	- 스레드 하나의 관점에서 출금을 보면 "1. 검증 단계"에서 확인한 잔액(balance) 1000원은 "2. 출금 단계"에서 계산을 끝마칠 때 까지 같은 1000원으로 유지되어야 한다.
	- 그래야 검증 단계에서 확인한 금액으로, 출금 단계에서 정확한 잔액을 계산할 수 있다.
	- 내가 사용하는 값이 **중간에 변경되지 않을 것이라는 가정**이 있다.

> 공유 자원
- 잔액(balance)은 여러 스레드가 함께 사용하는 **공유 자원**이다
- 따라서 출금 로직을 수행하는 중간에 **다른 스레드에서 얼마든지 값을 변경할 수 있다.**
- 여기서는 출금() 메서드를 호출할 때만 잔액(balance)의 값이 변경된다.
- 따라서 다른 스레드가 출금 메서드를 호출하면서, **사용중인 출금 값을 중간에 변경해버릴 수 있다.**

> 한 번에 하나의 스레드만 실행

>[!Question] 만약 출금() 메서드를 한 번에 하나의 스레드만 실행하도록 제한한다면?
- t1, t2 스레드가 함께 출금() 메서드를 호출하면
	- t1 스레드가 먼저 처음부터 끝까지 메서드를 완료하고
	- 그 다음에 t2 스레드가 처음부터 끝까지 메서드를 완료한다면?

>[!Success] 이렇게 하면 공유 자원인 balance를 한번에 하나의 스레드만 변경할 수 있다.
중간에 다른 스레드가 balance의 값을 변경하는 부분을 걱정하지 않아도 된다.


# synchronized 메서드
>[!info] 자바의 synchronized 키워드를 사용하면 한 번에 하나의 스레드만 실행할 수 있는 코드 구간을 만들 수 있다.

```java
public class BankAccountV2 implements BankAccount {  
  
    private int balance;  
  
    public BankAccountV2(int initialBalance) {  
        this.balance = initialBalance;  
    }  
  
    @Override  
    public synchronized boolean withdraw(int amount) {  
        log("거래 시작: " + getClass().getSimpleName());  
        // 잔고가 출금액 보다 적으면, 진행하면 안됨  
  
        log("[검증 시작] 출금액: " + amount + ", 잔액: " + balance);  
        if (balance < amount) {  
            log("[검증 실패] 출금액: " + amount + ", 잔액: " + balance);  
            return false;  
        }  
        // 잔고가 출금액 보다 많으면, 진행  
        log("[검증 완료] 출금액: " + amount + ", 잔액: " + balance);  
        sleep(1000); // 출금에 걸리는 시간으로 가정  
        balance = balance - amount;  
  
        log("[출금 완료] 출금액: " + amount + ", 잔액: " + balance);  
  
        log("거래 종료");  
        return true;  
    }  
  
    @Override  
    public synchronized int getBalance() {  
        return balance;  
    }  
}
```
```java
public class BankMain {  
    public static void main(String[] args) throws InterruptedException {  
        BankAccountV2 account = new BankAccountV2(1000);
        ...
	}
}
```
```
22:04:03.762 [       t1] 거래 시작: BankAccountV2
22:04:03.769 [       t1] [검증 시작] 출금액: 800, 잔액: 1000
22:04:03.769 [       t1] [검증 완료] 출금액: 800, 잔액: 1000
22:04:04.249 [     main] t1 state: TIMED_WAITING
22:04:04.250 [     main] t2 state: BLOCKED
22:04:04.777 [       t1] [출금 완료] 출금액: 800, 잔액: 200
22:04:04.778 [       t1] 거래 종료
22:04:04.781 [       t2] 거래 시작: BankAccountV2
22:04:04.781 [       t2] [검증 시작] 출금액: 800, 잔액: 200
22:04:04.782 [       t2] [검증 실패] 출금액: 800, 잔액: 200
22:04:04.783 [     main] 최종 잔액: 200
```
- t1이 withdraw() 메서드를 시작부터 완료까지 **모두 끝내고 나서**
  t2가 withdraw() 메서드를 수행하는 것을 확인할 수 있다.

> synchronized 분석

![[Pasted image 20241206220529.png]]
- 모든 객체(인스턴스)는 내부에 자신만의 락(lock)을 가지고 있다.
	- 모니터 락(monitor lock)이라고 부른다.
	- 객체 내부에 있고 우리가 확인하기는 어렵다.
- 스레드가 `synchronized` 키워드가 있는 메서드에 진입하려면 **반드시 해당 인스턴스의 락이 있어야 한다!**

![[Pasted image 20241206220708.png]]
- 스레드 t1이 먼저 synchronized 키워드가 있는 withdraw() 메서드를 호출한다.
- synchronized 메서드를 호출하려면 먼저 **해당 인스턴스의 락이 필요하다.**
- 락이 있으므로, 스레드 t1은 BankAccount(x001) 인스턴스에 있는 락을 획득한다.

![[Pasted image 20241206220834.png]]
- t1은 해당 인스턴스의 락을 획득했기 때문에 withdraw() 메서드에 진입할 수 있다.
- 스레드 t2도 withdraw() 메서드에 호출을 시도한다.
	- synchronized 메서드를 호출하려면 먼저 해당 인스턴스의 락이 필요하다.
	- t2는 BankAccount 인스턴스에 있는 락 획득을 시도한다.
	- 하지만 락이 없기 때문에 **t2 스레드는 락을 획득할 때까지 BLOCKED 상태로 대기**
	- t2 스레드의 상태는 RUNNABLE -> BLOCKED 상태로 변하고, **락을 획득할 때 까지 무한정 대기한다.**
- 참고로 BLOCKED 상태가 되면 락을 다시 획득하기 전까지는 계속 대기하고, CPU 실행 스케줄링에 들어가지 않는다.

![[Pasted image 20241206221122.png]]
![[Pasted image 20241206221147.png]]
- t1 스레드는 출금을 위한 검증 로직을 수행하고, 계산 결과인 200원을 잔액(balance)에 반영한다.

![[Pasted image 20241206221311.png]]
- t1 스레드는 메서드 호출이 끝나면 락을 반납한다.

![[Pasted image 20241206221329.png]]
- 인스턴스에 락이 반납되면, 락 획득을 대기하는 t2 스레드는 자동으로 락을 획득
- 이때 락을 획득한 t2스레드는 BLOCKED -> RUNNABLE 상태가 되고, 다시 코드를 실행

![[Pasted image 20241206221443.png]]
- t2 스레드는 해당 인스턴스의 락을 획득했기 때문에 withdraw() 메서드에 진입
- 출금을 위한 검증 로직을 수행할 때 조건을 만족하지 않으므로 false를 반환한다.

![[Pasted image 20241206221530.png]]
- t2 스레드는 메서드 호출이 끝나면, 락을 반납하면서 return 한다.

> 결과
> t1: 800원 출금 완료
> t2: 잔액 부족으로 출금 실패
> 원금 1000원, 최종 잔액 200원

>[!Success] 자바의 synchronized를 사용하면 한 번에 하나의 스레드만 실행하는 안전한 임계 영역 구간을 편리하게 만들 수 있다.

> 참고

**락을 획득하는 순서는 보장되지 않는다.**
만약 BankAccount 인스턴스의 withdraw()를 수많은 스레드가 동시에 호출한다면,
**1개의 스레드만 락을 획득하고 나머지는 모두 BLOCKED 상태가 된다.**
그리고 이후에 BankAccount 인스턴스에 락을 반납하면, 해당 인스턴스의 락을 기다리는 **수 많은 스레드 중에 하나의 스레드만 락을 획득**하고,
**락을 획득한 스레드만 BLOCKED -> RUNNABLE 상태가 된다.**
이때 어떤 순서로 락을 획득할지는 자바 표준에 정의되어 있지 않다.
따라서 **순서를 보장하지 않고**, **환경에 따라서 순서가 달라질 수 있다.**

또한, **volatile을 사용하지 않아도 synchronized 안에서 접근하는 변수의 메모리 가시성 문제는 해결된다.**

## synchronized 코드 블럭
- synchronized
	- 장점: 한 번에 하나의 스레드만 실행할 수 있다.
	- 단점: 여러 스레드가 동시에 실행하지 못하기 때문에 성능이 떨어질 수 있다.
- 따라서 synchronized를 통해 여러 스레드를 동시에 실행할 수 없는 코드 구간은 꼭! 필요한 곳으로 한정에서 설정해야 한다.

```java
public class BankAccountV3 implements BankAccount {  
  
    private int balance;  
  
    public BankAccountV3(int initialBalance) {  
        this.balance = initialBalance;  
    }  
  
    @Override  
    public boolean withdraw(int amount) {  
        log("거래 시작: " + getClass().getSimpleName());  
        // 잔고가 출금액 보다 적으면, 진행하면 안됨  
  
        synchronized (this) {  
            log("[검증 시작] 출금액: " + amount + ", 잔액: " + balance);  
            if (balance < amount) {  
                log("[검증 실패] 출금액: " + amount + ", 잔액: " + balance);  
                return false;  
            }  
  
            // 잔고가 출금액 보다 많으면, 진행  
            log("[검증 완료] 출금액: " + amount + ", 잔액: " + balance);  
            sleep(1000); // 출금에 걸리는 시간으로 가정  
            balance = balance - amount;  
  
            log("[출금 완료] 출금액: " + amount + ", 잔액: " + balance);  
        }  
        log("거래 종료");  
        return true;  
    }
}
```

## syncrhonized 동기화 정리
- 자바에서 동기화(syncrhonized)는 여러 스레드가 동시에 접근할 수 있는 자원(객체, 메서드)에 대해 일관성 있고 안전한 접근을 보장하기 위한 메커니즘이다.
- 동기화는 주로 멀티스레드 환경에서 발생할 수 있는 문제, 예를 들어 데이터 손상이나 예기치 않은 결과를 방지하기 위해 사용된다.

> 메서드 동기화: 메서드를 synchronized 로 선언해서, 메서드에 접근하는 스레드가 하나뿐이도록 보장한다.
```java
public synchronized void synchronizedMethod() { // 코드 }
```
> 블록 동기화: 코드 블록을 synchronized 로 감싸서, 동기화를 구현할 수 있다.
```java
public void method() { 
	synchronized(this) {
		 // 동기화된 코드 
	 } 
}
```

- 동기화를 사용하면 다음 문제들을 해결할 수 있다.
	- **경합 조건(Race condition)**: 두 개 이상의 스레드가 경쟁적으로 동일한 자원을 수정할 때 발생하는 문제
	- **데이터 일관성**: 여러 스레드가 동시에 읽고 쓰는 데이터의 일관성을 유지
- 동기화는 멀티스레드 환경에서 필수적인 기능이지만, 과도하게 사용할 경우 성능 저하를 초래할 수 있으므로 꼭 필요한 곳에 적절히 사용해야 한다.

> 지역 변수의 공유

```java
public class SyncTest2Main {  
    public static void main(String[] args) {  
        MyCounter myCounter = new MyCounter();  
  
        Runnable task = new Runnable() {  
  
            @Override  
            public void run() {  
                myCounter.count();  
            }  
        };  
  
        Thread thread1 = new Thread(task, "Thread-1");  
        Thread thread2 = new Thread(task, "Thread-2");  
  
        thread1.start();  
        thread2.start();  
  
    }  
  
    static class MyCounter {  
  
        public void count() {  
  
            int localValue = 0;  
  
            for (int i = 0; i < 1000; i++) {  
                localValue = localValue + 1;  
            }  
  
            log("결과: " + localValue);  
        }    }  
}
```
![[Pasted image 20241206223044.png]]
- localValue는 지역변수이다.
- 스택 영역은 각각의 **스레드가 가지는 별도의 메모리 공간**이다.
	- 이 메모리 공간은 **다른 스레드와 공유하지 않는다.**
	- 지역변수는 스레드의 개별 저장 공간인 스택 영역에 생성된다.
- 따라서 **지역 변수는 절대로! 다른 스레드와 공유되지 않는다.**
- 이 상황에서 synchronized를 사용해도 아무런 이득도 없다. **성능만 느려질 뿐이다!**
- 지역 변수를 제외한, 인스턴스의 멤버 변수(필드), 클래스 변수 등은 공유될 수 있다.


> final 필드

```java
public class Immutable {  
    private final int value;  
  
  
    public Immutable(int value) {  
        this.value = value;  
    }  
  
    public int getValue() {  
        return value;  
    }  
}
```
- 여러 스레드가 공유 자원에 접근하는 것 자체는 사실 문제가 되지 않는다.
- 진짜 문제는 **공유 자원을 사용하는 중간에 다른 스레드가 공유 자원의 값을 변경해버리기 때문에 발생한다.**
- 결국 변경이 문제가 되는 것이다.
- 여러 스레드가 접근 가능한 공유 자원이라도 **그 값을 아무도 변경할 수 없다면 문제 되지 않는다.** (모든 스레드가 항상 같은 값을 읽기 때문에)
- 필드에 final이 붙으면 어떤 스레드도 값을 변경할 수 없다.
- 따라서 멀티스레드 상황에 문제 없는 안전한 공유 자원이 된다.


> synchronized 장단점

- 장점
	- 프로그래밍 언어에 문법으로 제공
	- 아주 편리한 사용
	- 자동 잠금 해제
		- syncrhonized 메서드나 블록이 완료되면 자동으로 락을 대기중인 다른 스레드의 잠금이 해제된다.
- 단점
	- 무한 대기
		- BLOCKED 상태의 스레드는 락이 풀릴 때까지 무한 대기한다.
		- 특정 시간까지만 대기하는 타임아웃이 아니다.
		- 중간에 인터럽트도 불가능하다.
	- 공정성
		- 락이 돌아왔을 때, BLOCKED 상태의 여러 스레드 중에 어떤 스레드가 락을 획득할지 알 수 없다.
		- 최악의 경우 특정 스레드가 너무 오랜기간 락을 획득하지 못할 수 있다.
