> Java Thread API : Thread 정보 조회

# 현재 Thread 정보 조회
Java `Thread` 라이브러리에는 현재 실행 중인 `Thread`나 특정 `Thread`에 대한 정보 조회를 위한 다양한 메소드들을 지원한다.

## setName() and getName()
특정 `Thread`에 대한 이름을 반환해주는 메소드이다.
각종 디버깅 상황이나 `Multi Thread` 환경에서 특정 `Thread`를 식별하기 위해서는 식별할 수 있는 이름을 부여하여 `Thread`를 조회하는 것이 좋다.

Java에서는 이러한 점을 고려하여 `Thread` 생성자 인자 중 이름 값을 지정하거나 `setName()` 메소드를 지원하여 특정 `Thread` 의 이름을 설정 할 수 있다.

```java
...
Thread thread1 = new Thread(() -> {  
    ...  
}, "New Thread");  
  
Thread thread2 = new Thread(() -> {  
    ...
});  
thread2.setName("New Thread2");  
  
thread1.start();  
thread2.start();
...
```

> [!NOTE]
> **기본 Thread명**
> 
> `setName()` 이나 생성 시점에 별도로 이름을 주입하지 않은 경우에는, Java 에서 순차적으로 번호를 부여하여 이름을 생성해낸다
> - ex : thread-1, thread-2 등

## currentThread()
`Thread` 작업 수행 간 현재 CPU에 할당되어 작업중인 `Thread` 에 대한 객체를 반환해주는 메소드이다.

```java
...
System.out.println(Thread.currentThread().getName() + " Running...");
...
```

위와 같은 정적 메소드로 이루어져 있으며, 호출 결과에 따라 아래와 같이 현재 `Thread` 정보를 반환한다.
- 내부적으로 `native` 메소드를 호출하는 SystemCall의 일종이다.

![](images/Pasted%20image%2020240411164628.png)

## alive()
특정 `Thread`의 활성 상태 여부를 반환한다.
- *아직 종료되지 않고 `RUNNABLE` 이거나 대기 상태인 `Thread`들을 활성 상태로 간주*
