> 동기화 기법 : 상호 배제 : Mutual Exclusion : Mutex

# Mutual Exclusion
`Mutex` 라고도 부르며, 상호 배제 기법이라고도 부른다. `Multi Thread` 환경에서 공유 자원에 대한 경쟁을 방지하고자 lock 개념을 통해 `Critical Section` 접근을 제어하는 기법이다.

```java
do {
	...
	acquired(mutex)
		// critical section
	release(mutext)
	...
} while(true)
```

특정 `Thread` 가 `Critical Section`에 진입하기 위해 `acquired` 작업을 통해 lock 여부를 확인하며, 이미 lock 이 걸려 있을 경우, 대기 큐에 저장하도록 하며, lock 걸려 있지 않은 경우 lock을 걸고 `Critical Section`으로 넘어가도록 한다.

`Critical Section`을 벗어날 때에는 `release` 작업을 통해 lock을 해제하여 대기큐에서 대기중인 다른 `Thread` 에서 `Critical Section` 에 접근할 수 있도록 한다.

# Mutext 주의사항
`Mutual Exclusion` 을 통한 동기화 작업 시 아래 요소들을 주의해야 한다.
## Dead Lock
다수의 `Thread`에서 서로가 가진 lock을 기다리면서 상호 대기상태에 빠져 이후 작업을 수행할 수 없는 상태를 뜻하며, 잘못된 `Mutex`를 사용할 경우 발생할 수 있다.
## Priority Inversion
높은 우선순위를 지닌 `Thread`가 낮은 우선순의의 `Thread`의 lock을 기다리는 동안 대기되는 상태를 뜻하며, 이로 인해 우선순위가 높은데도 작업이 지연될 수 있다.

## Overhead
`Mutex`를 사용할 경우 여러 `Thread` 에서 lock 여부 검증 및 스케줄링을 통해 오버헤드가 발생하게 된다.
- 동기화 이슈에 대해서는 대체적으로 발생하며, 정합성이 우선인지 성능이 우선인지에 따라 동기화 작업을 진행한다.