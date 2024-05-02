> 동기화 기초 : Critical Section

# Critical Section
`Critical Section` 이란 임계 영역이라 하며, 동기화 작업 간 공유 자원에 대한 실질 적인 접근이 필요한 영역을 뜻하며, 이외에도 `Entry Section`, `Exit Section` 등이 존재한다.

```java
...
public void test() {
	lock.lock(); // Entry Section
	try {
		count++; // Critical Section
	} finally {
		lock.unlock(); // Exit Section
	}
}
...
```

`Entry Section`의 경우, `Critical Section` 에 진입하기 위해 요청하는 영역이며, 해당 영역에서 현재 `Critical Section`의 자원 접근 가능 여부에 따라 대기하거나 진입할 수 있다.

`Exit Section`의 경우, `Critical Section` 작업 이후 영역이며, 공유 자원에 대한 접근 종료를 전파한다.

## Critical Section Problem
`Critical Section` 영역을 수행중인 `Thread`외에 다른 `Thread`가 접근할 경우 동시성 이슈가 발생할 수 있기에, 아래 3가지를  충족하여 `Critical Section` 을 보호한다.

### Mutex : Mutual Exclusion
상호 배제 라고도 하며, 특정 `Critical Section`이 수행중인 경우 다른 `Thread`에서는 접근할 수 없도록 해야 한다.

### Progress
`Critical Section` 이 수행중인 `Thread`가 존재하지 않을 때 어떠한 `Thread`가 접근할지 적절히 선택해줘야 한다.

### Bounded Waiting
한정 대기라고도 하며, 대기중인 `Thread`들의 `Starvation`을 막기 위해 `Critical Section`을 점유하여 사용할 수 있는 횟수를 제한해야 한다.

> [!NOTE]
> **Race Condition**
> 
> 경쟁상태라고도 하며 `Critical Section` 에 접근한 `Thread`가 이미 존재할 때 다른 `Thread` 에서 접근하여 공유 데이터를 조작함으로써 발생하는 상태이다.
> 
> 위의 3 요소를 반영하여 해결해야 한다.

