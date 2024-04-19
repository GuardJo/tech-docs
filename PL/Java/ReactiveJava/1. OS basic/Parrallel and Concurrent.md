> 운영체제 기초 : 병렬성과 동시성

# 동시성 : Concurrent
![](images/Pasted%20image%2020240408160946.png)

하나의 CPU에서 여러 `Thread` 를 번갈아가며 수행하여, 사용자 입장에서 하나의 CPU에서 동시에 수행되는것처럼 보이는 작업을 뜻한다.

동시성 작업으로 동작하지 않는 경우 CPU는 하나의 `Thread` 작업이 종료될 때까지 다른 `Thread`에 대한 작업을 수행하지 못하며, 이는 CPU 자원에 대한 낭비로 이어질 수 있다.

그렇기에 동시성 작업을 통해 최대한 CPU를 활용하도록 하는 방안이 발전하였다.

# 병렬성 : Parallel
![](images/Pasted%20image%2020240408161146.png)

`parallel`은 `Concurrent`의 하위 개념으로, 여러 CPU에서 동시에 여러 `Thread`의 작업을 수행하는 것을 뜻한다.

각 CPU는 동일한 시점에 하나의 `Thread` 작업만 수행할 수 있으나, 이러한 CPU가 여러 개인 멀티 코어 프로세서 환경에서는 각 CPU 별로 동시에 여러 `Thread`를 수행할 수 있으며, 이에 따른 성능 향상이 주요 장점이다.

# Parallel과 Concurrent의 조합
현대에 와서는 대부분의 환경이 멀티 코어 프로세서 환경이기에 `Parallel`로 구성하나, 동시에 실행되는 `Process`의 `Thread` 수 또한 증가해왔기에 CPU의 개수보다 `Thread` 개수가 많은 경우가 대부분이며 이를, `Concurrent`를 활용하여 CPU 별로 작업 효율을 개선하고 있다.

## Java에서의 조합
### ThreadPoolExecutor
Java에서 지원하는 동시성 프로그래밍 관련 라이브러리 객체로, 지정된 CPU 수만큼 `Parallel` 기법으로 `Thread`들을 할당하며, 할당된 각 CPU는 할당 받은 `Thread`들을 `Concurrent` 기법으로 최대한 효율적으로 처리하도록 도와준다.

### ForkJoinPool
Java에서 지원하는 동시성 프로그래밍 관련 라이브러리 객체로 하나의 큰 작업 단위를 쪼개 각 CPU에 전달하며, 쪼개진 작업 (`Task`) 또한 정해진 만큼 계속 쪼갠 후 각 CPU에서 쪼개잔 `Task` 단위로 작업을 수행 후, 결과를 취합하는 방식으로 처리를 도와준다.

