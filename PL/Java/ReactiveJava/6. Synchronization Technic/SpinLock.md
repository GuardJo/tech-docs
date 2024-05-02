> 동기화 기법 : SpinLock

# Spin Lock
`Mutex`, `Semaphore`, `Monitor`와 같은 동기화 기법 중 하나이며, 앞선 기법들과는 다르게 `lock`을 획득하기 위해 대기하는 것이 아니라, 지속적으로 `lock`을 획득할 수 있는지 검사하며, `lock`을 획득할 수 있게 되면 검사를 마치고 `Critical Section`에 진입한다.

## 동작 방식

```c
volatile int lock = 0;
void critical() {
	while (test_and_set(&lock) == 1) {
		// Critical Section
	}

	lock = 0;
}
...
int test_and_set(int* lock) {
	int oldValue;
	oldValue = *lock;
	*lock = 1;

	return oldValue;
}
...
```

`Thread`가 `Critical Section`에 접근할 때 `SpinLock`을 시도하며, `SpinLock` 은 내부적으로 현재 `lock` 상태에 따라 1일 경우 이미 다른 `Thread`가 `lock`을 획득한 것으로 간주하여 무한 루프를 통해 `lock`이 0이 될때 까지 상태를 지속적으로 체크한다.

이후 `lock`을 할당 받아 `Critical Section`을 수행한 이후에 다시 `lock`을 0으로 변경하여 다른 `SpinLock`상태의 `Thread`가 무한루프를 끝내고 접근할 수 있도록 한다.

> [!NOTE]
> **Busy Waiting**
> 
> `Busy Waiting`은 `SpinLock`에서 `lock`을 획득하지 못하고 무한루프를 진행중인 `Thread`를 뜻하며, 대기가 아닌 무한루프를 수행중이기에 CPU의 자원을 지속적으로 사용하는 상태이다.

## SpinLock 장/단점
### 장점
`Thread`가 `Busy Waiting`상태로 `lock` 얻기를 기다리기 때문에 CPU를 계속 점유하고 있으며, 이 덕분에 CPU가 `Thread`를 변경하며 수행하는 `Context Switching` 비용을 아낄 수 있다.

또한 무한루프를 통해 지속적으로 `lock` 획득을 시도하기 때문에 실제 `lock`이 해제될 경우 대기 없이 바로 `lock`을 점유할 수 있다.

### 단점
`Thread`가 `Busy Waiting`상태인 것 자체가 CPU 자원을 낭비하는 요소이며, 이에 따라 CPU가 1개인 `Single Core` 환경에서는 `SpinLock` 을 사용하는 것 자체가 해당 `Process`에 모든 처리를 일임하여 다른 작업을 수행할 수 없게 한다.

또한 CPU 자원을 지속적으로 사용함으로 인해, 대기 시간이 길거나 `Critical Section` 에 대한 점유 경쟁이 치열한 경우에는 오히려 `Context Switching`보다 많은 비용을 사용하게 된다.

