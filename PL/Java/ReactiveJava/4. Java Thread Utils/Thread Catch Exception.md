> Java Thread의 예외 처리

# Thread 예외 처리
기본적으로 `Thread`의 `run()` 메소드는 예외를 던질 수 없도록 약속되어 있다.
그렇기에 `Thread` 내부에서 발생하는 예외들은 내부에서 처리되어야 하며 `RuntimeException` 같은 non-check-exception 의 경우에는 `Thread` 외부에서는 예외 처리를 할 수 없게 되어 있다.

Java에서는 이와 같은 상황에서 `Thread`의 비정상 종료나 예외를 제어하기 위해 `UncaughtExceptionHandler` 인터페이스를 제공한다.

# UncaughtExceptionHandler

```java
@FunctionalInterface  
public interface UncaughtExceptionHandler {  
	void uncaughtException(Thread t, Throwable e);  
}
```

`Thread`에서 제어되지 못한 예외 반환 시 호출되는 인터페이스로, 위와 같은 구조로 이루어져 있으며, 인자로 주어지는 `Thread` 객체와 `Throwable` 객체를 통해 어떠한 `Thread` 에서 어떠한 예외가 발생했는지 식별 할 수 있다.
- `Thread` 내부에서 예외가 발생하면 `UncaughtException` 이 발생하고, 해당 `Exception`을 `UncaughtExceptionHandler` 에서 처리하게 된다.

## Handler 설정
Java에서는 `Thread` 예외 처리를 위한 `UncaughtExceptionHandler` 설정을 아래 두 메소드로 지원하고 있다.

### setDefaultUncaughtExceptionHandler()
`Thread` 객체의 정적 메소드로써, 전체 `Thread` 에 전역으로 `UncaughtExceptionHandler`를 지정한다.

```java
...
Thread thread1 = new Thread(() -> {  
    System.out.println(Thread.currentThread().getName() + " Start");  
    throw new RuntimeException("This is a test");  
});  
  
Thread.UncaughtExceptionHandler handler = (t, e) -> {  
    System.out.println(t.getName() + " caught " + e);  
};  
  
Thread.setDefaultUncaughtExceptionHandler(handler);  
  
thread1.start();
...
```

위와 같이 모든 `Thread` 에 대해 전역 핸들러를 지정하여 예외 발생 시 해당 핸들러에서 처리할 수 있도록 해준다.

### setUncaughtExceptionHandler(UncaughtExceptionHadnler)
특정 `Thread` 에 대해 인자로 주어진 `UncaughtExceptionHandler`를 지정해준다.
이는 앞서 전역으로 설정된 핸들러보다 높은 우선순위를 지닌다.

```java
...
thread2.setUncaughtExceptionHandler((t, e) -> {  
    System.out.println(t.getName() + "의 예외는 " + e);  
});  
  
thread1.start();  
thread2.start();
...
```

위와 같이 특정 `Thread` 에만 특정 핸들러를 지정할 수 있다.