 > Thread Group

# Thread Group
Java 에서는 `ThreadGroup` 이라는 객체를 통해 여러 `Thread`들을 그룹화하여 관리할 수 있도록 관련 객체 및 메소드들을 제공한다.

최초 애플리케이션 실행 시 JVM에서는 `System` 이라는 이름의 `ThreadGroup` 이 생성되며 해당 `ThreadGroup` 내 JVM에서 생성하는 각종 `Daemon Thread` 들이 포함된다.

이후 main() 메소드 실행 간 비즈니스 로직을 처리하는 `User Thread`들을 담은 `Main` 이라는 이름의 `ThreadGroup` 이 생성되며 `Main Thread` 하위에 생성되는 `Thread` 및 `ThreadGroup`들은 일반적으로 해당  `ThreadGroup` 하위에 포함된다.

## ThreadGroup 생성
```java
public class ThreadGroupExample {  
    public static void main(String[] args) {  
        ThreadGroup mainGroup = Thread.currentThread().getThreadGroup();  
        ThreadGroup subGroup = new ThreadGroup(mainGroup, "Sub Group");  
  
        Runnable runnable = () -> {  
            ThreadGroup group = Thread.currentThread().getThreadGroup();  
  
            System.out.println(Thread.currentThread().getName() + " in " + group.getName() + " of " + group.getParent().getName());  
            System.out.println();  
        };  
  
        Thread thread = new Thread(runnable);  
        Thread subThread = new Thread(subGroup, runnable, "Sub Thread");  
  
        System.out.println(mainGroup.getName());  
        thread.start();  
        subThread.start();  
    }  
}
```

위와 같이 신규 `ThreadGroup` 을 생성할 수 있으며, 인자로 특정 `ThreadGroup`을 지정하면 해당 그룹 하위에 포함되며, 인자를 생략할 경우에는 해당 객체를 생성한 `Thread` 가 포함된 `ThreadGroup` 에 포함된다.
- *특정 `Thread`의 `ThreadGroup`을 조회하기 위한 `getThreadGroup()` 메소드 또한 지원됨*

## ThreadGroup 우선순위 제한
`ThreadGroup`의 경우 하위 `Thread` 들의 최대 우선순위 값을 제한할 수 있으며, 이를 통해 해당 `ThreadGroup` 에서 신규 생성되는 `Thread`들의 우선순위 값을 제어할 수 있다.

```java
public class NestedThreadGroupExample {  
    public static void main(String[] args) throws InterruptedException {  
        ThreadGroup group = new ThreadGroup("group");  
        ThreadGroup child = new ThreadGroup(group, "child");  
  
        Runnable updateParentPriority = () -> {  
            System.out.println(Thread.currentThread().getName() + ": updateParentPriority");  
            Thread.currentThread().getThreadGroup().setMaxPriority(3);  
        };  
  
        Runnable updateChildPriority = () -> {  
            System.out.println(Thread.currentThread().getName() + ": updateChildPriority");  
            Thread.currentThread().getThreadGroup().setMaxPriority(2);  
        };  
  
        Thread thread = new Thread(group, updateParentPriority);  
        Thread thread2 = new Thread(child, updateChildPriority);  
  
        thread.start();  
        thread2.start();  
  
        thread.join();  
        thread2.join();  
  
        // 이미 Thread의 작업중에 ThreadGroup의 priority가 변경된건 반영되지 않음  
        System.out.println(thread.getName() + " priority = " + thread.getPriority());  
        System.out.println(thread2.getName() + " priority = " + thread2.getPriority());  
  
        // ThreadGroup의 priority가 변경된 후에 생성된 Thread들은 제한 사항이 적용됨  
        Thread newThread = new Thread(group, () -> {  
            System.out.println(Thread.currentThread().getName() + " group = " + Thread.currentThread().getThreadGroup());  
        });  
  
        newThread.start();  
        newThread.join();  
  
        System.out.println(newThread.getName() + " priority = " + newThread.getPriority());  
    }  
}
```

위와 같이 `ThreadGroup`의 `setMaxPriority()` 메소드를 통해 해당 `ThreadGroup`의 최대 우선순위 값을 제한할 수 있다.

단, 해당 메소드 호출 시점에 이미 생성되어 있는 `Thread`에는 적용되지 않으며 호출 이후에 신규 생성되는 `Thread`들부터 적용된다.

## ThreadGroup을 통한 일괄 Interrupt
`ThreadGroup` 에 `interrupt()` 메소드 호출을 통해 해당 `ThreadGroup` 내 활성 상태의 모든 `Thread`들에게 `interrupt()` 를 호출 할 수 있다.

```java
Runnable exceptionRun = () -> {  
    while (!Thread.currentThread().isInterrupted()) {  
        System.out.println(Thread.currentThread().getName() + " is Running...");  
    }  
  
    System.out.println(Thread.currentThread().getName() + " is Stopping...");  
};  
  
Thread thread = new Thread(exceptionRun);  
Thread thread2 = new Thread(exceptionRun);  
  
thread.start();  
thread2.start();  
Thread.sleep(1000);  
Thread.currentThread().getThreadGroup().interrupt();  
System.out.println("All Interrupted!");
```

위와 같이 두 `Thread` 가 interrupt 발생 시 종료되게끔 구성된 `Runnable`을 실행 시킬 때, 해당 `ThreadGroup`에서 `interrupt()`를 호출 함으로써 하위 `Thread` 인 두 `Thread`가 interrupt를 받아 종료되게 된다.