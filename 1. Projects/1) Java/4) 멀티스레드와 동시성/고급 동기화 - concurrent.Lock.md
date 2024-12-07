# LockSupport 1
- synchronized 단점
	- 무한 대기: BLOCKED 상태의 스레드는 락이 풀릴 때 까지 무한대기
		- 특정 시간까지만 대기하는 타임아웃 X
		- 중간에 인터럽트 X
	- 공정성
		- 락이 들어왔을 때 BLOCKED 상태의 여러 스레드 중에 어떤 스레드가 락을 획득할지 알 수 없다.
		- 최악의 경우 특정 스레드가 너무 오랜기간 락을 획득하지 못할 수 있다.

>[!Success] 이런 문제를 해결하기 위해 java.util.concurrent 동시성 문제 해결 패키지 추가

## LockSupport 기능
- LockSupport는 스레드를 `WAITING` 상태로 변경한다.
	- `WAITING` 상태는 누가 깨워주기 전까지는 계속 대기한다.
	- CPU 실행 스케줄링에 들어가지 않는다.
- 기능
	- park(): 스레드를 `WAITING` 상태로 변경
		- 스레드를 대기 상태로 둔다.
	- parkNanos(nanos):: 스레드를 나노초 동안만 `TIMED_WAITING` 상태로 변경
		- 지정한 나노초가 지나면 `TIMED_WAITING` 상태에서 `RUNNABLE` 상태로 변경
	- unpark(thread): `WAITING` 상태의 대상 스레드를 `RUNNABLE` 상태로 변경

```java
public class LockSupportMainV1 {  
    public static void main(String[] args) {  
        Thread thread1 = new Thread(new ParkTask(), "Thread-1");  
        thread1.start();  
  
        // 잠시 대기하여 Thread-1이 park 상태에 빠질 시간을 준다.  
        sleep(1000);  
        LockSupport.unpark(thread1); // 1. unpark 사용  
    }  
  
    static class ParkTask implements Runnable {  
  
        @Override  
        public void run() {  
            log("park 시작");  
            LockSupport.park(); // WAITING 상태로 변경  
            log("park 종료, state: " + Thread.currentThread().getState());  
            log("인터럽트 상태: " + Thread.currentThread().isInterrupted());  
        }    }  
}
```
```
23:43:03.264 [ Thread-1] park 시작
23:43:04.256 [ Thread-1] park 종료, state: RUNNABLE
23:43:04.257 [ Thread-1] 인터럽트 상태: false
```

![[Pasted image 20241206234347.png]]
- main 스레드가 Thread-1을 `start()`하면 Thread-1은 `RUNNABLE` 상태가 된다.
- Thread-1은 `LockSupport.park()`를 호출한다.
	- Thread-1은 `RUNNABLE -> WAITING` 상태가 되면서 대기한다.
- main 스레드가 Thread-1을 `unpark()`로 깨운다.
	- Thread-1은 `WAITING` 상태에서 `RUNNABLE` 상태로 변한다.
- 이처럼 LockSupport는 특정 스레드를 `WAITING` 상태로, 또 `RUNNABLE` 상태로 변경할 수 있다.
>[!Question] 대기 상태로 바꾸는 LockSupport.park()는 매개변수가 없는데, 실행 가능 상태로 바꾸는 LockSupport.unpark(thread1)은 왜 특정 스레드를 지정하는 매개변수가 있을까?
>실행중인 스레드는 LockSupport.park()를 호출해서 스스로 대기 상태에 빠질 수 있지만, 대기 상태의 스레드는 자신의 코드를 실행할 수 없기 때문에 외부 스레드의도움을 받아야 깨어날 수 있다.


## 인터럽트 사용
> WAITING 상태의 스레드에 인터럽트가 발생하면 WAITING -> RUNNABLE 상태로 변경

```java
public class LockSupportMainV1 {  
    public static void main(String[] args) {  
        Thread thread1 = new Thread(new ParkTask(), "Thread-1");  
        thread1.start();  
  
        // 잠시 대기하여 Thread-1이 park 상태에 빠질 시간을 준다.  
        sleep(1000);  
        thread1.interrupt(); // 2. interrupt 사용  
    }  
  
    static class ParkTask implements Runnable {  
  
        @Override  
        public void run() {  
            log("park 시작");  
            LockSupport.park(); // WAITING 상태로 변경  
            log("park 종료, state: " + Thread.currentThread().getState());  
            log("인터럽트 상태: " + Thread.currentThread().isInterrupted());  
        }    }  
}
```
```
23:51:49.481 [ Thread-1] park 시작
23:51:50.468 [ Thread-1] park 종료, state: RUNNABLE
23:51:50.469 [ Thread-1] 인터럽트 상태: true
```
- WAITING -> interrupt -> RUNNABLE
- 스레드의 인터럽트 상대도 true인 것을 확인

# LockSupport 2
## 시간 대기
- parkNanos(nanos) : 스레드를 나노초 동안만 TIMED_WAITING 상태로 변경한다. 지정한 나노초가 지나면 TIMED_WAITING 상태에서 빠져나와서 RUNNABLE 상태로 변경된다.
- 참고로 밀리초 동안만 대기하는 메서드는 없다. parkUntil(밀리초) 라는 메서드가 있는데, 이 메서드는 특정 에포크(Epoch) 시간에 맞추어 깨어나는 메서드이다. 정확한 미래의 에포크 시점을 지정해야 한다.

```java
public class LockSupportMainV2 {  
    public static void main(String[] args) {  
        Thread thread1 = new Thread(new ParkTask(), "Thread-1");  
        thread1.start();  
  
        // 잠시 대기하여 Thread-1이 park 상태에 빠질 시간을 준다.  
        sleep(1000);  
        log("Thread-1 state: " + thread1.getState());  
    }  
  
    static class ParkTask implements Runnable {  
  
        @Override  
        public void run() {  
            log("park 시작, 2초 대기");  
            LockSupport.parkNanos(2000_000000); // parkNanos 사용  
            log("park 종료, state: " + Thread.currentThread().getState());  
            log("인터럽트 상태: " + Thread.currentThread().isInterrupted());  
        }    }  
}
```
```
23:57:20.059 [ Thread-1] park 시작, 2초 대기
23:57:21.052 [     main] Thread-1 state: TIMED_WAITING
23:57:22.066 [ Thread-1] park 종료, state: RUNNABLE
23:57:22.068 [ Thread-1] 인터럽트 상태: false
```
- 여기서는 스레드를 깨우기 위한 unpark()를 사용하지 않는다.
- parkNanos(nanos)를 사용하면 지정한 시간 이후에 스레드가 깨어난다.
- 1초 = 1000밀리초(ms)
- 1밀리초 = 1,000,000나노초(ns)
- 2초 = 2,000,000,000나노초(ns)
![[Pasted image 20241207000238.png]]
- Thread-1은 parkNanos(2초)를 사용해서 2초간 TIMED_WAITING 상태에 빠진다.
- Thread-1은 2초 이후에 시간 대기 상태(TIMED_WAITING)를 빠져나온다.

# BLOCKED vs WAITING
WAITING 상태에 특정 시간까지만 대기하는 기능이 포함된 것이 TIMED_WAITING이다.

- 인터럽트
	- BLOCKED 상태는 인터럽트가 걸려도 대기 상태를 빠져나오지 못한다.
	- WAITING, TIMED_WAITING 상태는 인터럽트가 걸리면 대기 상태를 빠져나온다.
		- RUNNABLE 상태로변한다.
- 용도
	- BLOCKED 상태는 자바의 syncrhonized에서 락을 획득하기 위해 대기할 때 사용
	- WAITING, TIMED_WAITING 상태는 스레드가 특정 조건이나 시간 동안 대기할 때 발생하는 상태이다.
	- `WAITING` 상태는 다양한 상황에서 사용된다.
		- Thread.join(), LockSupprot.park(), Object.wait()와 같은 메서드 호출 시 WAITING 상태가 된다.
	- `TIMED_WAITING` 상태는 Thread.sleep(ms), Object.wait(long timeout), Thread.join(long millis), LockSupport.parkNanos(ns) 등과 같은 시간 제한이 있는 대기 메서드를 호출할 때 발생한다.
- 대기(WAITING) 상태와 시간 대기 상태(TIMED_WAITING)는 서로 짝이 있다.
	- Thread.join(), Thread.join(logn millis)
	- Thread.park(), Thread.parkNanos(long millis)
	- Object.wait(), Object.wait(long timeout)

> BLOCKED, WAITING, TIMED_WAITING 상태는 모두 스레드가 대기하며, 실행 스케줄링에 들어가지 않기 때문에, CPU 입장에서 보면 실행하지 않는 비슷한 상태이다.
- BLOCKED 상태는 synchronized에서만 사용하는 특별한 대기 상태이다.
- WAITING, TIMED_WAITING 상태는 범용적으로 활용할 수 있는 대기 상태이다.

# LockSupport 정리
- LockSupport를 사용하면 스레드를 WAITING, TIMED_WAITING 상태로 변경할 수 있고, 또 인터럽트를 받아서 스레드를 깨울 수도 있다. (RUNNABLE)
- synchronized 단점
	- 무한 대기: BLOCKED 상태의 스레드는 락이 풀릴 때까지 무한 대기한다.
		- 특정 시간까지만 대기하는 타임아웃 X
		  -> parkNanos()를 사용하면 특정 시간까지 대기할 수 있음.
		- 중간에 인터럽트 X
		  -> park(), parkNanos()는 인터럽트를 걸 수 있음.

>[!Success] 이처럼 LockSupport를 활용하면, 무한 대기하지 않는 락 기능을 만들 수 있다.

# ReentrantLock - 이론
>[!Info] 자바는 1.0부터 존재한 synchronized와 BLOCKED 상태를 통한 임계 영역 관리의 한계를 극복하기 위해 자바 1.5부터 Lock 인터페이스와 ReentrantLock 구현체를 제공한다.

- synchronized 단점
	- 무한 대기: BLOCKED 상태의 스레드는 락이 풀릴 때 까지 무한대기
		- 특정 시간까지만 대기하는 타임아웃 X
		- 중간에 인터럽트 X
	- 공정성
		- 락이 들어왔을 때 BLOCKED 상태의 여러 스레드 중에 어떤 스레드가 락을 획득할지 알 수 없다.
		- 최악의 경우 특정 스레드가 너무 오랜기간 락을 획득하지 못할 수 있다.

> 무한 대기 문제 해결

```java
public interface Lock {
	void lock();
	void lockInterruptibly() throws InterruptedException;
	boolean tryLock();
	boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
	void unlock();
	Condition newCondition();
}
```
- Lock 인터페이스는 동시성 프로그래밍에서 쓰이는 안전한 임계 영역을 위한 락을 구현하는데 사용된다.

- void lock()
	- **락을 획득**한다.
	- 만약 다른 스레드가 이미 락을 획득했다면, **락이 풀릴 때까지 현재 스레드는 대기**(WAITING)한다. **이 메서드는 인터럽트에 응답하지 않는다.**
- void lockInterruptibly()
	- 락 획득을 시도하되, **다른 스레드가 인터럽트 할 수 있도록 한다**.
	- 만약 다른 스레드가 이미 락을 획득했다면, 현재 스레드는 **락을 획득할 때까지 대기**(WAITING)한다.
	- 대기 중에 인터럽트가 발생하면 `InterruptedException`이 발생하며 락 획득을 포기한다.
- boolean tryLock()
	- 락 획득을 시도하고, **즉시 성공 여부를 반환**한다.
	- 만약 다른 스레드가 이미 락을 획득했다면 `false`를 반환하고,
	  그렇지 않으면 락을 획득하고 `true`를 반환한다.
- boolean tryLock(long time, TimeUnit unit)
	- **주어진 시간 동안 락 획득을 시도**한다.
	- 주어진 시간 안에 락을 획득하면 `true`를 반환한다.
	- 주어진 시간이 지나도 락을 획득하지 못한 경우 `false`를 반환한다.
	- 이 메서드는 대기 중 인터럽트가 발생하면 `InterruptedException`이 발생하며, 락 획득을 포기한다.
- void unlock()
	- **락을 해제**한다.
	- 락을 해제하면 락 획득을 **대기 중인 스레드 중 하나가 락을 획득**할 수 있게 된다.
	- **락을 획득한 스레드가 호출**해야 하며, 그렇지 않으면 `IllegalMonitorStateException`이 발생한다.
	- ex) 식당안에 있는 손님이 밥을 먹고 나간다. 식당에 자리가 하나 난다. 기다리는 손님께 이런 사실을 알려주어야 한다. 기다리던 손님중 한 명이 식당에 들어간다.
- Condition newCondition()
	- `Condition` 객체를 생성하여 반환한다.
	- `Condition` 객체는 락과 결합되어 사용되며, 스레드가 특정 조건을 기다리거나 신호를 받을 수 있도록 한다.
	- 이는 `Object` 클래스의 `wait`, `notify`, `notifyAll` 메서드와 유사한 역할을 한다.

>[!Success] 이 메서드들을 사용하면 고수준의 동기화 기법을 구현할 수 있다.
>Lock 인터페이스는 synchronized 블록보다 더 많은 유연성을 제공하며,
>특히 락을 특정 시간 만큼만 시도하거나, 인터럽트 가능한 락을 사용할 때 유용하다.
>다양한 메서드를 통해 synchronized의 단점인 무한 대기 문제도 깔끔하게 해결할 수 있다.


> 공정성 문제 해결

락이 돌아왔을 때 BLOCKED 상태의 여러 스레드 중에 어떤 스레드가 락을 획득할 지 알 수 없다. 최악의 경우 특정 스레드가 너무 오랜기간 락을 획득하지 못할 수 있다.

>[!Success] Lock 인터페이스의 대표적인 구현체로 ReentrantLock이 있는데, 이 클래스는 스레드가 공정하게 락을 얻을 수 있는 모드를 제공한다.


```java
public class ReentrantLockEx {  
  
    // 비공정 모드 락  
    private final Lock nonFairLock = new ReentrantLock();  
  
    // 공정 모드 락  
    private final Lock fairLock = new ReentrantLock(true); 
     
    public void nonFairLockTest() {  
        nonFairLock.lock();  
        try {  
            // 임계 영역  
        } finally {  
            nonFairLock.unlock();  
        }    }  
    public void fairLockTest() {  
        fairLock.lock();  
        try {  
            // 임계 영역  
        } finally {  
            fairLock.unlock();  
        }    }  
    }
```

> 비공정 모드 (Non-fair mode)
- 비공정 모드는 ReentrantLock의 기본 모드이다.
- 이 모드에서는 락을 먼저 요청한 스레드가 락을 먼저 획득한다는 보장이 없다.
- 락을 풀었을 때, 대기 중인 스레드 중 아무나 락을 획득할 수 있다.
- 락을 빨리 획득할 수 있지만, 특정 스레드가 장기간 락을 획득하지 못할 가능성도 있다.

- 특징
	- 성능 우선: 락을 획득하는 속도가 빠르다.
	- 선점 가능: 새로운 스레드가 기존 대기 스레드보다 먼저 락을 획득할 수 있다.
	- 기아 현상 가능성: 특정 스레드가 계속해서 락을 획득하지 못할 수도 있다.

> 공정 모드 (Fair mode)
- 생성자에서 true를 매개변수로 전달하면 된다.
   `new ReentrantLock(true);`
- 락을 요청한 순서대로 스레드가 락을 획득할 수 있게 된다.
- 먼저 대기한 스레드가 먼저 락을 획득하게 되어 스레드 간의 공정성을 보장한다.
	- 그러나 이로 인해 성능이 저하될 수 있다.

- 특징
	- 공정성 보장: 대기 큐에서 먼저 대기한 스레드가 락을 먼저 획득한다.
	- 기아 현상 방지: 모든 스레드가 언젠가 락을 획득할 수 있게 보장한다.
	- 성능 저하: 락을 획득하는 속도가 느려질 수 있다.

> 정리
- 비공정 모드는 성능을 중시하고, 스레드가 락을 빨리 획득할 수 있지만, 특정 스레드가 계속해서 락을 획득하지 못할 수 있다.
- 공정 모드는 스레드가 락을 획득하는 순서를 보장하여 공정성을 중시하지만, 성능이 저하될 수 있다.

# ReentrantLock - 활용
```java
public class BankAccountV4 implements BankAccount {  
  
    private int balance;  
  
    private final Lock lock = new ReentrantLock();  
  
    public BankAccountV4(int initialBalance) {  
        this.balance = initialBalance;  
    }  
  
    @Override  
    public boolean withdraw(int amount) {  
        log("거래 시작: " + getClass().getSimpleName());  
        // 잔고가 출금액 보다 적으면, 진행하면 안됨  
  
        lock.lock(); // ReentrantLock 이용하여 lock 걸기.  
  
        try {  
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
        } finally {  
            lock.unlock(); // ReentrantLock 이용하여 lock 해제  
        }  
  
        log("거래 종료");  
        return true;  
    }  
  
    @Override  
    public synchronized int getBalance() {  
        lock.lock(); // ReentrantLock 이용하여 lock 걸기.  
        try {  
            return balance;  
        } finally {  
            lock.unlock(); // ReentrantLock 이용하여 lock 해제  
        }  
    }  
}
```
```java
01:30:23.754 [       t2] 거래 시작: BankAccountV4
01:30:23.763 [       t1] [검증 시작] 출금액: 800, 잔액: 1000
01:30:23.763 [       t1] [검증 완료] 출금액: 800, 잔액: 1000
01:30:24.242 [     main] t1 state: TIMED_WAITING
01:30:24.242 [     main] t2 state: WAITING
01:30:24.768 [       t1] [출금 완료] 출금액: 800, 잔액: 200
01:30:24.769 [       t1] 거래 종료
01:30:24.769 [       t2] [검증 시작] 출금액: 800, 잔액: 200
01:30:24.769 [       t2] [검증 실패] 출금액: 800, 잔액: 200
01:30:24.769 [     main] 최종 잔액: 200
```
- private final Lock lock = new ReentrantLock()을 사용하도록 선언
- synchronized(this) 대신에 lock.lock()을 사용해서 락을 건다.
	- lock() -> unlock()까지는 안전한 임계 영역이 된다.
- **임계 영역이 끝나면 반드시! 락을 반납해야 한다.**
	- 그렇지 않으면 대기하는 스레드가 락을 얻지 못한다.
	- 따라서 lock.unlock()은 반드시 finally 블럭에 작성해야 한다.
	- 이렇게 하면 검증에 실패해서 중간에 return을 호출해도 또는 중간에 예상치 못한 예외가 발생해도 lock.unlock()이 반드시 호출된다.

> 실행 결과 분석

![[Pasted image 20241207013746.png]]
- ReentrantLock 내부에는 락과 락을 얻지 못해 대기하는 스레드를 관리하는 대기 큐가 존재한다.
- 여기서 이야기 하는 락은 객체 내부에 있는 모니터 락이 아니다.
  ReentrantLock이 제공하는 기능이다.

![[Pasted image 20241207013909.png]]
- t1: ReentrantLock에 있는 락을 획득한다.
- **락을 획득하는 경우 RUNNABLE 상태가 유지**되고, 임계 영역의 코드를 실행할 수 있다.

![[Pasted image 20241207014009.png]]
![[Pasted image 20241207014020.png]]
- t1: 임계 영역의 코드를 실행
- t2: ReentrantLock에 있는 락의 획득을 시도한다. 하지만 락이 없다.

![[Pasted image 20241207014129.png]]
- t2: 락을 획득하지 못하면 WAITING 상태가 되고, 대기 큐에서 관리된다.
	- LockSupport.park()가 내부에서 호출된다.
(참고)
- tryLock(long time, TimeUnit unit)와 같은 시간 대기 기능을 사용하면 TIMED_WAITING이 되고, 대기 큐에서 관리된다.

![[Pasted image 20241207014303.png]]
- t1: 임계 영역의 코드를 수행 완료 -> balance=200

![[Pasted image 20241207014339.png]]
- t1: 임계 영역을 수행하고 나면 lock.unlock()을 호출한다.
	1. t1: 락을 반납한다.
	2. t1: 대기 큐의 스레드를 하나 깨운다.
		LockSupport.unpark(thread)가 내부에서 호출된다.
	3. t2: RUNNABLE 상태가 되면서 깨어난 스레드는 락 획득을 시도한다.
		- 이때 락을 획득하면 `lock.lock()`을 빠져나오면서 대기 큐에서도 제거된다.
		- 이때 락을 획득하지 못하면 다시 대기 상태가 되면서 대기 큐에 유지된다.
			- 락 획득을 시도하는 잠깐 사이에 새로운 스레드가 락을 먼저 가져갈 수 있다. (스레드 락 획득 순서를 보장하지 않는다.)
			- 공정 모드의 경우 대기 큐에 먼저 대기한 스레드가 먼저 락을 가져간다.

![[Pasted image 20241207015254.png]]
- t2: 락을 획득한 t2 스레드는 RUNNABLE 상태로 임계 영역을 수행한다.

![[Pasted image 20241207015338.png]]
- t2: 잔액(200)이 출금액(800)보다 적으므로 검증 로직을 통과하지 못한다.
  return false가 호출된다.
- 이때 finally 구문이 있으므로 finally 구문으로 이동한다.

![[Pasted image 20241207015440.png]]
- t2: lock.unlock()을 호출해서 락을 반납하고, 대기 큐의 스레드를 하나 깨우려고 시도한다.
- 대기 큐에 스레드가 없으므로 이때는 깨우지 않는다.

![[Pasted image 20241207015544.png]]
- 완료 상태

# ReentrantLock - 대기 중단
ReentrantLock을 사용하면 락을 무한 대기하지 않고, 중간에 빠져나오는 것이 가능하다.
심지어 락을 얻을 수 없다면, 기다리지 않고 즉시 빠져나오는 것도 가능하다.

- boolean tryLock()
	- 락 획득을 시도하고, 즉시 성공 여부를 반환한다.
	- 만약 다른 스레드가 이미 락을 획득했다면 false를 반환하고,
	  그렇지 않으면 락을 획득하고 true를 반환한다.
- boolean tryLock(long time, TimeUnit unit)
	- 주어진 시간 동안 락 획득을 시도한다.
	- 주어진 시간 안에 락을 획득하면 true를 반환한다.
	- 주어진 시간이 지나도 락을 획득하지 못한 경우 false를 반환한다.
	- 대기 중 인터럽트가 발생하면 InterruptedException이 발생하며 락 획득을 포기

> tryLock()
```java
public class BankAccountV5 implements BankAccount {  
  
    private int balance;  
  
    private final Lock lock = new ReentrantLock();  
  
    public BankAccountV5(int initialBalance) {  
        this.balance = initialBalance;  
    }  
  
    @Override  
    public boolean withdraw(int amount) {  
        log("거래 시작: " + getClass().getSimpleName());  
        // 잔고가 출금액 보다 적으면, 진행하면 안됨  
  
        if (!lock.tryLock()) {  
            log("[진입 실패 이미 처리중인 작업이 있습니다.]");  
            return false;  
        }  
        try {  
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
        } finally {  
            lock.unlock(); // ReentrantLock 이용하여 lock 해제  
        }  
  
        log("거래 종료");  
        return true;  
    }  
  
    @Override  
    public synchronized int getBalance() {  
        lock.lock(); // ReentrantLock 이용하여 lock 걸기.  
        try {  
            return balance;  
        } finally {  
            lock.unlock(); // ReentrantLock 이용하여 lock 해제  
        }  
    }  
}
```
```
02:22:05.231 [       t1] 거래 시작: BankAccountV5
02:22:05.231 [       t2] 거래 시작: BankAccountV5
02:22:05.233 [       t2] [진입 실패 이미 처리중인 작업이 있습니다.]
02:22:05.236 [       t1] [검증 시작] 출금액: 800, 잔액: 1000
02:22:05.236 [       t1] [검증 완료] 출금액: 800, 잔액: 1000
02:22:05.724 [     main] t1 state: TIMED_WAITING
02:22:05.725 [     main] t2 state: TERMINATED
02:22:06.239 [       t1] [출금 완료] 출금액: 800, 잔액: 200
02:22:06.240 [       t1] 거래 종료
02:22:06.240 [     main] 최종 잔액: 200
```
- lock.tryLock()을 사용한다.
- 락을 획득할 수 없으면 바로 포기하고 대기하지 않는다.
- 락을 획득할 수 없다면 false를 반환한다.

> tryLock(시간)

```java
public class BankAccountV6 implements BankAccount {  
  
    private int balance;  
  
    private final Lock lock = new ReentrantLock();  
  
    public BankAccountV6(int initialBalance) {  
        this.balance = initialBalance;  
    }  
  
    @Override  
    public boolean withdraw(int amount) {  
        log("거래 시작: " + getClass().getSimpleName());  
        // 잔고가 출금액 보다 적으면, 진행하면 안됨  
  
        try {  
            if (!lock.tryLock(500, TimeUnit.MICROSECONDS)) {  
                log("[진입 실패 이미 처리중인 작업이 있습니다.]");  
                return false;  
            }  
        } catch (InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
        try {  
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
        } finally {  
            lock.unlock(); // ReentrantLock 이용하여 lock 해제  
        }  
  
        log("거래 종료");  
        return true;  
    }  
  
    @Override  
    public synchronized int getBalance() {  
        lock.lock(); // ReentrantLock 이용하여 lock 걸기.  
        try {  
            return balance;  
        } finally {  
            lock.unlock(); // ReentrantLock 이용하여 lock 해제  
        }  
    }  
}
```
```
02:30:37.043 [       t1] 거래 시작: BankAccountV6
02:30:37.043 [       t2] 거래 시작: BankAccountV6
02:30:37.047 [       t2] [진입 실패 이미 처리중인 작업이 있습니다.]
02:30:37.052 [       t1] [검증 시작] 출금액: 800, 잔액: 1000
02:30:37.052 [       t1] [검증 완료] 출금액: 800, 잔액: 1000
02:30:37.517 [     main] t1 state: TIMED_WAITING
02:30:37.518 [     main] t2 state: TERMINATED
02:30:38.057 [       t1] [출금 완료] 출금액: 800, 잔액: 200
02:30:38.058 [       t1] 거래 종료
02:30:38.060 [     main] 최종 잔액: 200
```
- lock.tryLock(500, TimeUnit.MICROSECONDS
	- 락이 없을 때 락을 대기할 시간을 지정한다.
	- 해당 시간이 지나도 락을 얻지 못하면 false를 반환하면서 해당 메서드를 빠져나온다.
	- 스레드의 상태는 대기하는 동안 TIMED_WAITING이 되고, 대기 상태를 빠져나오면 RUNNABLE이 된다.
- t1: 먼저 락을 획득하고 임계 영역 수행
- t2: lock.tryLock(0.5초)을 호출하고 락 획득을 시도한다.
	- 락이 없으므로 0.5초간 대기한다.
	- 이때 t2는 TIMED_WAITING 상태가 된다.
	- 내부에서는 LockSupport.parkNanos(시간)이 호출된다.
- t2: 대기 시간인 0.5초간 락을 획득하지 못했다.
	- lock.tryLock(시간)에서 즉시 빠져나온다.
	- 이때 false가 반환된다.
	- TIMED_WAITING -> RUNNABLE이 된다.
- t2: "\[진입 실패 이미 처리중인 작업이 있습니다.]"를 출력하고 false를 반환하면서 메서드를 종료한다.
- t1: 임계 영역의 수행을 완료하고 락을 반납한다.

>[!Success] Lock 인터페이스와 ReentrantLock 덕분에 synchronized의 단점인 무한 대기와 공정성 문제를 극복하고, 더욱 유연하고 세밀한 스레드 제어가 가능하게 되었다.

