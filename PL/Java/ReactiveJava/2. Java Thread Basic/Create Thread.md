> Java Thread 라이브러리 : Thread 생성

# Java에서의 Thread 생성 모델

현재 Java의 `Thread` 관리 모델은 `OneToOne` 모델로써 JVM에서 생성되는 `Thread` 마다 실제 OS의 `Kernel Level Thread`에 매핑되는 구조이며, 이에 따라 `Thread` 생성 비용이 높지만, 멀티프로세서 환경에서 병렬처리가 가능하게 되었다.

> [!NOTE]
> 
> **Java Green Thread**
> 
> 과거 버전에서는 `ManyToOne` 모델을 사용하여 JVM에서 생성하는 다수의 `Thread`들은 하나의 `Kernel Level Thread`에 매핑되어 단일 CPU에서 동작이 수행되었다.

# Thread 생성 방법
Java의 `Thread` 라이브러리를 통해 `Thread`를 생성하는 방법은 크게 아래 두 경우로 구분된다.

## Thread 객체 생성

```java
static class WorkerThread extends Thread {  
    @Override  
    public void run() {  
        System.out.println(Thread.currentThread().getName() + " is running");  
    }  
}
...
public static void main(String[] args) {  
    Thread t1 = new WorkerThread();  
    t1.start();
}
```

위와 같이 Java에서 제공하는 `Thread` 객체를 상속받아서 신규 `Thread`를 생성할 수 있다.
- `run()` 메소드를 재정의 하여 해당 `Thread`의 작업을 정의할 수 있다.
- 생성된  `Thread` 객체는 `start()` 메소드를 통해 `run()` 메소드를 내부적으로 호출한다.

## Runnable 구현체 생성

```java
static class WorkerTask implements Runnable {  
  
    @Override  
    public void run() {  
        System.out.println(Thread.currentThread().getName() + " is running");  
    }  
}
...
public static void main(String[] args) {  
    Thread t2 = new Thread(new WorkerTask());  

    t2.start();  
}
```

위와 같이 Java에서 제공하는 `Runnable` 인터페이스에 대한 구현체를 구현하여 `Thread` 객체에 인자로 전달하여 `Thread`를 생성할 수 있다.
- 해당 `Thread`의 작업 정의는 `Runnable` 인터페이스에 정의된 `run()` 메소드에서 수행된다.


