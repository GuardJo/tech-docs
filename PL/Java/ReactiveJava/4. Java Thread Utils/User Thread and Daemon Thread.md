> 사용자 Thread와 데몬 Thread

# User Thread
`Thread` 의 종류 중 하나로 애플리케이션 시작 시 구동되는 `Main Thread` 를 필두로 하여 `Main Thread` 하위에 생성되는 모든 `Thread` 들을 기본적으로 `User Thread`라고 한다.

`User Thread` 하위에 생성되는 `Thread` 들 또한 기본적으로 `User Thread` 이다.

`Multi Thread` 환경에서 `User Thread`는 다른 `Thread`들과는 별개의 생명주기를 지니고 있어, 할당된 작업이 끝나면 알아서 종료된다.

# Daemon Thread
`Thread`의 종류 중 하나로 애플리케이션 시작 시 JVM에서 자체적으로 몇가지의 `Daemon Thread`를 구동한다.

사용자가 `Daemon Thread`를 생성하기 위해서는 별도로 제공하는 메소드를 통해 구성할 수 있다.

`Multi Thread` 환경에서 `Daemon Thread`는 애플리케이션 종료 시 실행되며, 전체 `User Thread` 종료 시 JVM이 `Daemon Thread`들을 종료시킨다.

## setDaemon()
특정 `Thread`를 `DaemonThread`로 지정할 수 있는 메소드로 해당 `Thread` 작업 전에 수행되어야 한다.
- *작업 중에 호출할 경우 예외가 발생함*

```java
...
Thread thread = new Thread(() -> {  
    for (int i = 0; i < 5; i++) {  
        System.out.println(Thread.currentThread().getName() + ": " + i);  
        try {  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
    }  
});  
  
thread.setDaemon(true);  
  
thread.start();  
System.out.println("MainThread Finished");
...
```

위와 같이 사용할 수 있으며 `setDaemon()` 에 의해 `Thread` 내부의 daemon 상태 값이 true가 되는 경우 `Daemon Thread`로 지정된다.