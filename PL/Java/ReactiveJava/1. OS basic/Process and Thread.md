> 운영체제 기초 : Process 와 Thread

# Process란?
OS가 설치된 프로그램을 실행하여 실제 수행되는 작업의 최소 단위를 `Process` 라고 한다.

OS가 메모리에 `Process`를 올리며, 해당 `Process` 는 할당된 메모리를 기반으로 작업을 수행하게 된다.

## Process 메모리 구조
![](images/Pasted%20image%2020240408152528.png)

`Process`의 메모리 구조는 크게 위와 같이 이루어져 있으며, 일반적으로 각 `Process` 끼리는 어떠한 데이터도 서로 영향을 주지 않는 독립적인 형태로 작업을 수행한다.
- *단, IPC나 공유 메모리 등의 통신 기법을 통해 접근이 가능하긴 함*

### 동적 영역
`Process` 동작에 따라 가변적으로 적재되는 데이터들에 대한 영역으로 크게 `Stack 영역` 과 `Heap 영역`이 존재한다.

- `Stack영역` : 함수 안에서 선언된 지역 변수나, 매개변수, 반환 값 등에 대한 데이터가 저장됨
- `Heap영역` : 배열이나 Collection 등의 자료 구조 초기화 및 사용자에 의해 할당되는 데이터들이 저장됨

### 정적 영역
`Process` 시작 시 구성되는 데이터들이 저장되는 영역이며, `Process` 종료 시까지 생명주기를 함께 한다.

- `Data영역` : `Process`가 실행되면서 저장되는 전역 변수, `static 변수`  등이 저장됨
- `code영역` : `Process`가 수행하는 실제 코드들이 저장됨

# Thread란?
OS로부터 실행되는 `Process`를 통해 할당된 자원으로 실제 코드를 수행하는 주체이다.
하나의 `Process`에는 최소 하나 이상의 `Thread`가 존재한다.

## Thread 메모리 구조
![](images/Pasted%20image%2020240408153533.png)

`Thread`는 해당 `Thread`가 포함된 `Process`의 `Code영역`, `Data영역`, `Heap영역` 들을 서로 공유할 수 있으며, 이에 따라 해당 영역에서 동시성 이슈들이 발생할 수 있다.
- 단, 매개변수, 지역 변수 등이 저장되는 `Stack영역`은 각 `Thread`별로 독립적이다.

> [!NOTE]
> **Thread와 CPU**
> 
> `Process`는 해당 작업을 위해 자원을 RAM으로부터 할당받는 역할이며, 실제 코드를 수행하는 `Thread`는 CPU를 할당받아 해당 CPU에서 작업을 수행하게 된다.
> 
> 이 때 CPU는 오직 하나의 `Thread` 작업만을 수행할 수 있으며, `Concurrency` 작업 수행을 위해서는 하나의 CPU에서 여러 `Thread`를 번갈아가면서 수행하며, 이를 OS의 `Scheduler`가 지정된 알고리즘에 따라 수행하도록 돕는다.

