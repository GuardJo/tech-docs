> 동기화 기법 : Semaphore

# Semaphore
`Mutex`와 같은 동기화 기법 중 하나로, `Critical Section` 에서의 곻유 자원에 대한 접근 제어를 특정 신호 값을 기반으로 하는 신호 전달 방식의 기법이다.

크게 접근 가능 수를 뜻하는 `정수 변수 S`와, 진입 여부를 결정하는 `P 연산`,  대기 중인 `Thread`를 활성화 하는 `V 연산`으로 이루어져 있다.

# 동작 방식

| S 변수                                     | P 연산 (wait)                                                     | V 연산 (signal)                                                    |
| ---------------------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------- |
| 정수형 변수                                   | S가 1씩 감소                                                        | S가 1씩 증가                                                         |
| 공유 가능 자원의 개수로, 해당 개수만큼 `Thread` 접근을 허용한다 | `Thread`가 `Critical Section`에 진입하기 전에 수행하여, 공유 가능 자원 개수를 감소시킨다. | `Thread`가 `Critical Section` 수행 후 나올 때 수행하여 공유 가능 자원 개수를 증가 시킨다. |
```java
do {
	P(S):
		//Critical Section
	V(S):
		...
}
```

`Semaphore`의 경우 위와 같은 방식으로 동작하며, S가 0이 되기 전까지는 `Critical Section` 에 대한 접근을 허용하며 `P연산` 수행을 통해 S가 0이 된 이후에 남은 `Thread`들은 대기 상태로 지정해둔다.

이후 `S연산`을 통해 `Critical Section`을 빠져나온 만큼 S의 개수를 회복 시켜 이후 대기중인 다른 `Thread`들이 접근할 수 있도록 한다.
# Semaphore 유형
`Semaphore`는 크게 S의 최대값에 따라 `Binary Semaphore`와 `Counting Semaphore`로 구분된다.

## Binary Semaphore
`Semaphore`의 `S변수`가 최대 1인 `Semaphore`로써 신호 전달 체계이지만 최대 하나의 `Thread`만 `Critical Section`에 접근이 가능하기에 기능적으로 lock 방식의 `Mutex`와 동일하다.
- *최대 접근 가능 `Thread`가 1개이기에 lock을 설정하거나 해제하는 `Thread`가 동일해야 함*

## Counting Semaphore
`Semaphore`의 `S변수`가 2개 이상인 `Semaphore`로써 `Critical Section`에 접근 가능한 최대 `Thread` 수를 제한하여 `Treadpool`이나 DB Connection 등, 자원을 제한해야할 때 주로 사용된다.
- *최대 접근 가능 `Thread`가 n개이기에 lock 을 설정하는 `Thread`와 해제하는 `Thread`가 다를 수 있음*
# Mutex와 Semaphore 차이
## 동작 방식의 차이
`Mutex`의 경우 `Critical Section` 에 대한 접근이 오직 하나의 `Thread`만 가능하지만, `Semaphore`의 경우 최대 n개의 `Thread`가 동시에 접근 할 수 있다.
- *단, `Binary Semaphore`는 1개의 `Thread`만 접근 가능함*
## 소유권
`Mutex`는 단일 `Thread` 만 `Critical Section` 에 접근하기에 lock에 대한 설정 및 해제를 동일한  `Thread`에서 진행한다.

이에 반해 `Semaphore`의 경우 n개의 `Thread`가 동시 접근할 수 있기에 lock에 대한 설정 및 해제가 각각 다른 `Thread`에서 진행될 수 있다.
## 초기값
`Mutex`의 경우 lock이 잠겨있는 상태에서 초기에 시작되며, `Semaphore`는 초기값을 설정할 수 있게 초기값에 따라 `Thread` 접근 여부를 결정할 수 있다.
## 사용 목적
`Mutex`는 주로 상호배제를 목적으로 사용되며, 하나의 `Critical Section`에 오직 하나의 `Thread `만 접근해야만 할 때 사용된다.

이에 반해 `Semaphore`는 DB Connection이나 Thread pool 관리 등 한정된 자원에서 `Critical Section`에 접근해야할 때 사용된다.

> [!NOTE]
> **JAVA에서의 동기화 지원**
> 
> Java 에서는 `Mutex`를 기반으로 고도화 시킨 `Monitor`와 `Semaphore` 객체를 지원한다.