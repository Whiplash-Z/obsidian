```java
public class VolatileFlagMain {  
    public static void main(String[] args) {  
        MyTask task = new MyTask();  
        Thread t = new Thread(task, "work");  
        log("runFlag = " + task.runFlag);  
        t.start();  
  
        sleep(1000);  
        log("runFlag를 false로 변경 시도");  
        task.runFlag = false;  
        log("runFlag = " + task.runFlag);  
        log("main 종료");  
    }  
  
    static class MyTask implements Runnable {  
  
        boolean runFlag = true;  
        // volatile boolean runFlag = true;  
  
        @Override  
        public void run() {  
            log("task 시작");  
            while (runFlag) {  
                // runFlag가 false로 변하면 탈출  
            }  
            log("task 종료");  
        }    }  
}
```
```
14:18:58.411 [     main] runFlag = true
14:18:58.412 [     work] task 시작
14:18:59.418 [     main] runFlag를 false로 변경 시도
14:18:59.418 [     main] runFlag = false
14:18:59.418 [     main] main 종료
```

![[Pasted image 20241206142237.png]]


> 기대하는 실행 결과
```java
14:18:58.411 [     main] runFlag = true
14:18:58.412 [     work] task 시작
14:18:59.418 [     main] runFlag를 false로 변경 시도
14:18:59.418 [     main] runFlag = false
14:18:59.418 [     main] task 종료
14:18:59.418 [     main] main 종료
```

> 실제 실행 결과
```java
14:18:58.411 [     main] runFlag = true
14:18:58.412 [     work] task 시작
14:18:59.418 [     main] runFlag를 false로 변경 시도
14:18:59.418 [     main] runFlag = false
14:18:59.418 [     main] main 종료
```

- 분명히 runFlag를 false로 변경한 것을 콘솔에 출력해서 확인했는데,
  task 종료가 출력이 되지 않는다.
- 그리고 자바 프로그램도 멈추지 않고 계속 실행된다.
  (work 스레드가 while문에서 빠져나오지 못하고 있다.)

>[!Question] 도대체 어떻게 된 일일까?

> 일반적으로 생각하는 메모리 접근 방식

![[Pasted image 20241206143309.png]]
![[Pasted image 20241206143357.png]]
1. main 스레드는 runFlaga 값을 false로 설정한다.
2. 이때 메인 메모리의 runFlag 값이 false로 설정된다.
3. work 스레드는 while(runFlag)를 실행할 때 runFlag의 **데이터를 메인 메모리에서 확인**
4. runFlag의 값이 false이므로 while문을 탈출하고, "task 종료"를 출력한다.

> 실제 메모리의 접근 방식

>[!Info] CPU는 처리 성능을 개선하기 위해 중간에 캐시 메모리라는 것을 사용한다.

![[Pasted image 20241206143644.png]]
- 메인 메모리는 CPU 입장에서 보면 거리도 멀고, 속도도 상대적으로 느리다.
- CPU 연산은 매우 빠르기 때문에 CPU 연산의 빠른 성능을 따라가려면 CPU에 매우 가까이에 매우 빠른 메모리가 필요한데, 이것이 바로 캐시 메모리이다.
- 현대의 CPU 대부분은 코어 단위로 캐시 메모리를 각각 보유하고 있다.

- 각 스레드가 runFlag의 값을 사용하려면 CPU는 이 값을 효율적으로 처리하기 위해 먼저 runFlag를 캐시 메모리에 불러온다.
- 그리고 이후에는 캐시메모리에 있는 runFlag를 사용하게 된다.


![[Pasted image 20241206143901.png]]
![[Pasted image 20241206144046.png]]
1. main 스레드는 runFlag를 false로 설정한다.
2. 이때 캐시 메모리의 runFlag가 false로 설정된다.
	-> **캐시 메모리의 runFlag 값만 변경된다. 메인 메모리에 이 값이 즉시 반영되지 않는다.**
- main 스레드가 runFlag의 값을 변경해도 **CPU 코어1이 사용하는 캐시 메모리의 runFlag 값만 false로 변경된다.**
- work 스레드가 사용하는 CPU 코어2의 캐시 메모리의 runFlag 값은 **여전히 true이다.**
- work 스레드의 경우 while(runFlag[true])가 만족하기 때문에 while문을 계속 반복해서 수행한다.

>[!Question] 캐시 메모리에 있는 runFlag의 값이 언제 메인 메모리에 반영될까?
- 이 부분에 대한 정답은 "알 수 없다"이다.
	- CPU 설계 방식과 종류에 따라 다르다.
	- 극단적으로 보면 평생 반영되지 않을 수도 있다.
- 메인 메모리에 반영을 해도, 문제는 여기서 끝이 아니다.
- 메인 메모리에 반영된 runFlag 값을 work 스레드가 사용하는 캐시 메모리에 불러와야 한다.
![[Pasted image 20241206144503.png]]
>[!Question] 메인 메모리에 변경된 runFlag 값이 언제 CPU 코어2의 캐시 메모리에 반영될까?
- 이 부분에 대한 정답도 "알 수 없다"이다.
	- CPU 설계 방식과 종류에 따라 다르다.
	- 극단적으로 보면 평생 반영되지 않을 수도 있다.
- 언젠가 CPU 코어2의 캐시 메모리에 runFlag 값을 불러오게 되면 work 스레드가 확인하는 runFlag의 값이 false가 되므로 while문을 탈출하고, "task 종료"를 출력한다.

>[!Info] 이처럼 멀티스레드 환경에서 한 스레드가 변경한 값이 다른 스레드에서 언제 보이는지에 대한 문제를 메모리 가시성 (memory visibility)이라 한다.


>[!Question] 그렇다면 한 스레드에서 변경한 값이 다른 스레드에서 즉시 보이게 하려면 어떻게 해야할까?

# volatile, 메모리 가시성
캐시 메모리를 사용하면 CPU 처리 성능을 개선할 수 있다.
하지만 때로는 이런 성능 향상 보다는, 여**러 스레드에서 같은 시점에 같은 데이터를 보는 것**이 더 중요할 수 있다.

> 해결 방안

성능을 약간 포기하는 대신에, 값을 읽고 쓸 때 모두 **메인 메모리에 직접 접근하면 된다.**
자바에서는 `volatile`이라는 키워드로 이런 기능을 제공한다.

```java
public class VolatileFlagMain {  
    public static void main(String[] args) {  
        MyTask task = new MyTask();  
        Thread t = new Thread(task, "work");  
        log("runFlag = " + task.runFlag);  
        t.start();  
  
        sleep(1000);  
        log("runFlag를 false로 변경 시도");  
        task.runFlag = false;  
        log("runFlag = " + task.runFlag);  
        log("main 종료");  
    }  
  
    static class MyTask implements Runnable {  
  
        // boolean runFlag = true;  
        volatile boolean runFlag = true;  
  
        @Override  
        public void run() {  
            log("task 시작");  
            while (runFlag) {  
                // runFlag가 false로 변하면 탈출  
            }  
            log("task 종료");  
        }    }  
}
```
```
14:50:32.209 [     main] runFlag = true
14:50:32.210 [     work] task 시작
14:50:33.216 [     main] runFlag를 false로 변경 시도
14:50:33.216 [     work] task 종료
14:50:33.216 [     main] runFlag = false
14:50:33.217 [     main] main 종료
```

![[Pasted image 20241206145336.png]]
- `volatile`이라는 키워드를 사용하면 runFlag에 대해서 **캐시 메모리를 사용하지 않고**, 값을 읽거나 쓸 때 **항상 메인메모리에 직접 접근한다.**
- 여러 스레드에서 값을 읽고 써야 한다면, volatile 키워드를 사용하면 된다.
- 단, 캐시 메모리를 사용할 때보다 성능이 느려지는 단점이 있기 때문에 꼭! 필요한 곳에서만 사용하는 것이 좋다.

>[!Success] volatile 또는 스레드 동기화 기법(sysnchronized, ReentrantLock)을 사용하면 메모리 가시성 문제가 발생하지 않는다.







