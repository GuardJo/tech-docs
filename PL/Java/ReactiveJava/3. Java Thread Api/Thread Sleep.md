 > Java Thread API : Sleep

# sleep()
Java의 `Thread` 관련 제공 메소드로써, 현재 `Thread`를 일정 시간동안 `TIMED_WAITING` 상태로 전환한다.

## 메소드 구조
```java
/**  
 * Causes the currently executing thread to sleep (temporarily cease 
 * execution) for the specified number of milliseconds, subject to 
 * the precision and accuracy of system timers and schedulers. The thread 
 * does not lose ownership of any monitors. 
 * * @param  millis  
 *         the length of time to sleep in milliseconds  
 * * @throws  IllegalArgumentException  
 *          if the value of {@code millis} is negative  
 * * @throws  InterruptedException  
 *          if any thread has interrupted the current thread. The 
 *          <i>interrupted status</i> of the current thread is  
 *          cleared when this exception is thrown. 
 */
 public static native void sleep(long millis) throws InterruptedException;
```

위와 같은 형태의 `native` 메소드이기에 인자로 주어진 밀리초 만큼을 대기 시간으로 하여 OS의 System Call을 요청한다.

이에 따라 `sleep()` 메소드는 User Mode 에서 Kernel Mode로의 전환 작업이 수반되며, `Context Switching` 작업이 발생한다.

> [!NOTE]
> **sleep(0)**
> 
> 주로 `sleep()` 메소드에 1 이상의 대기 시간을 부여하면, 일반적으로 `TIMED_WAITING` 상태로 넘어가 다른 `Thread`에게 CPU를 할당하게 되며, 이는 `Context Switching`이 발생하는 부분이다.
> 
> 허나 `sleep(0)` 을 호출 할 경우 곧바로 `TIMES_WAITING` 상태로 전환되지 않고, 현재 `Thread` 와 동일한 `priority` 이면서 `RUNNABLE` 상태인 `Thread` 가 존재하지 않을 경우 곧바로 이어서 현재 `Thread` 작업을 마저 수행하게 된다.
> 
> 즉, 이러한 경우에는 호출한 `Thread`의 작업 모드가 `Kernel Mode`로 전환은 되지만, `Context Switching`은 발생하지 않는다.

## 사용 예시
```java
...
Thread sleepThread = new Thread(() -> {  
    try {  
        System.out.println("Enter after 5 sec");  
        Thread.sleep(5000);  
        System.out.println("Hello Thread!");  
    } catch (InterruptedException e) {  
        System.out.println("Thread interrupted");  
        throw new RuntimeException(e);  
    }  
});  
  
sleepThread.start();
...
```

위와 같이 실행중인 `Thread` 에 대해 특정 시간만큼 대기 상태로 전환할 수 있으며, `sleep()` 메소드 정의에 따라 `InterruptedException` 에 대한 예외처리를 해주어야 한다.

`InterruptedException`은 다른 `Thread`가 현재 `Thread`에게 `interrupt()` 를 호출 할 경우 발생하게 되며, 대기 시간에 관계 없이 예외처리에 의해 `RUNNABLE` 상태로 전환된다.