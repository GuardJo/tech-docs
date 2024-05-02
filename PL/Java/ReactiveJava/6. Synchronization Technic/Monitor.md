> 동기화 기법 : Monitor

# Monitor
`Monitor` 란 `Mutex`나 `Semaphore`보다 더 고도화된 동기화 기법으로, 큰 틀에서는 `Mutex`와 같은 상호 배제 기법에 추가적으로 `Cooperation` 기법을 지원한다.

Java에서는 JVM 내 `Monitor` 기능이 별도로 내장되어 있으며, 여러 `Thread`가 `Critical Section`에 진입하려고 할 때 `Monitor`에서 동기화 기능을 제공한다. 
- `synchronized` 키워드 사용 시 제공됨

## Cooperation
`Monitor`에서 제공하는 기능으로 `Condition Variable` 을 통해 여러 `Thread` 간의 상호작용이 필요한 경우 일관성과 안정성을 보장하는 기법이다.
### Condition Variable
조건 변수라고 하며, Java의 최상위 객체인 Object 객체의 기본 제공 메소드들인 `wait()`, `notify()`, `notifyAll()` 이 이에 해당한다.

## Monitor Set
Java의 `Monitor`에는 `Muti Thread` 환경에서 상호 작용을 조절하기 위한 `Entry Set`과 `Wait Set`이 존재한다.

### Entry Set
`Monitor`의 `lock`을 획득하기 하기 위해 대기중인 `Thread`들을 모아둔 Set으로 특정 `Thread`가 이미 `lock`을 획득하여 `Critical Section` 진입한 경우 나머지 `Thread`들은 `Entry Set`에 대기하며 추후 `lock`을 획득하는 과정을 진행한다.

### Wait Set
`Monitor`에서 `Conditional Variable` 에 따라 특정 조건이 만족할 때 까지 대기하는 Set으로 `Thread`는 `Wait Set`에 저장될 때  `lock`을 해제한다.
- *다른 `Thread`나 조건 만족에 의해 깨어날 경우 `Entry Set`으로 이동함*

# Monitor Conditional Variable 종류
`Conditional Variable`을 통해 상호 협력 중인 두 `Thread` 사이에 `wait()`, `notify()` 메소드 호출을 통해 한 `Thread`는 대기중으로, 다른 `Thread`는 깨우는 상태로써 두 `Thread`가 모두 `Monitor` 에 접근하게 된다.

이 때  `Monitor`는 접근하는 `Thread`들에 대하여 우선순위를 부여하는 방법을 크게 아래 두 종류로 나눈다.

## Signal And Wait
대기중인 `Thread`와 깨운 `Thread` 사이에 다른 `Thread`가 `Monitor`를 소유할 수 없도록 모든 수행들을 원자적으로 수행한다.

대기에서 깨어난 `Thread`가 `lock`을 획득 한 후 모든 작업을 마치고 `lock`을 해제하면 깨운 `Thread`가 `lock`을 획득 한 후 작업을 진행한다.

## Signal And Continue
Java `Monitor`의 동작 방식이며, 깨우는 `Thread`는 본인의 작업이 완전히 종료될 때까지 `lock`을 소유하고 있으며, 작업 종료 시에 `lock`을 해제하여 `Entry Set`에 대기중인 `Thread` 들에게 경쟁을 유도한다.