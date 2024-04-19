> Java Thread 작업 종료

# ~~stop(), suspend()~~
Java `Thread`에서 기존에는 작업 중지를 위한 `stop()`, `suspend()` 메소드를 지원했으나, 이미 작업중인 `Thread`를 강제로 종료하게 되면서 발생되는 Side Effect 들의 문제로 인해 현재는 deprecate 되었다.

해당 메소드들이 deprecate 되면서 `Thread` 를 종료하기 위해서는 작업중인 `run()` 메소드 내에서의 분기 처리로 작업을 해야 하며, 이에 대해서 크게 특정 플래그 변수를 바라보는 방법과 `interrupt()` 를 통한 작업처리 방식 등이 존재한다.

# Flag Variable를 통한 중지
`Thread` 를 중지하도록 별도의 플래그 변수를 선언하여 해당 변수에 따른 조건 처리로 작업을 중지시킬 수 있다.

단 `Multi Thread`환경에서 동일한 플래그 변수에 대한 접근 시 정합성을 위해 동시성 이슈 처리가 가능하게 `volitile` 이나 `Atomic`타입 객체로 선언해야 한다.

## Flag Variable 예시
### volatile 키워드 사용
```java
...
private volatile static boolean RUNNING = true;  
  
public static void main(String[] args) throws InterruptedException {  
    Thread thread = new Thread(() -> {  
        while (RUNNING) {  
            System.out.println(Thread.currentThread().getName() + " Running...");  
        }  
    });  
  
    thread.start();  
    Thread.sleep(1000);  
    RUNNING = false;  
    thread.join();  
    System.out.println("Thread is stopped");  
}
...
```

위와 같이 플래그 변수를 지정하여, 해당 플래그 변수의 값에 따라 내부 작업 반복 여부 등을 지정할 수 있으며, 해당 플래그 변수를 업데이트함으로써 작업 중지 등이 가능하다.

단, 위의 경우에는 플래그 변수의 정합성을 맞추기 위해 `volatile` 키워드를 사용하였다.

### Atomic 객체 사용
```java
private static final AtomicBoolean running = new AtomicBoolean(true);  
  
public static void main(String[] args) throws InterruptedException {  
    Thread thread = new Thread(() -> {  
        while (running.get()) {  
            System.out.println(Thread.currentThread().getName() + " Running...");  
        }  
    });  
  
    thread.start();  
    Thread.sleep(1000);  
    running.set(false);  
    thread.join();  
    System.out.println("Thread is stopped");  
}
```

위와 같이 선언된 플래그 변수를 사용함으로써 작업 제어가 가능하며, 해당 경우에는 `AtomicBoolean` 객체를 사용하여 플래그 변수의 정합성을 맞추었다.

# interrupt를 통한 중지
플래그 변수 외에도 `Thread`에서 기본 구성 변수인 `interrupt` 를 통해 작업을 제어할 수도 있다.

```java
public static void main(String[] args) throws InterruptedException {  
    Thread thread = new Thread(() -> {  
        while (!Thread.currentThread().isInterrupted()) {  
            System.out.println(Thread.currentThread().getName() + " is Running...");  
        }  
    });  
  
    thread.start();  
    Thread.sleep(1000);  
    thread.interrupt();  
    thread.join();  
    System.out.println("Thread Stopped");  
}
```

위와 같이 `Thread` 의 작업 분기 기준을 `interrupt` 상태 값을 기준으로 하여 `interrupted()` 메소드 호출을 통해 `Thread` 작업을 제어할 수 있다.

## InterruptedException 활용
```java
Thread sleepThread = new Thread(() -> {  
    boolean isRunning = true;  
    while (isRunning) {  
        try {  
            System.out.println(Thread.currentThread().getName() + " is Running...");  
            Thread.sleep(1);  
        } catch (InterruptedException e) {  
            isRunning = false;  
        }  
    }  
});
```

`sleep()` 등 대기중인 `Thread` 에게 `interrupt()` 를 통해 `InterruptedException` 을 던질 수 있으며, 이에 대한 처리 작업을 통해서도 `Thread` 작업에 대한 제어가 가능하다.