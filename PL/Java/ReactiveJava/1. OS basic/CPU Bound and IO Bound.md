> 운영체제 기초 : CPU Bound vs I/O Bound

# Process의 작업 수행
`Process` 가 실행되면 해당 `Process`는 일반적인 작업 수행 시에는 할당된 메모리를 기반으로 CPU가 작업을 수행하며, 때때로 File 이나 입출력 데이터 처리 시에는 CPU가 아닌  I/O가 작업을 수행하게 된다.

그렇기에 `Process`는 일반적으로 I/O 작업과 CPU 작업이 번갈아가며 발생하고, 이때마다 `Context Switching`을 통해 I/O 작업 간 CPU가 놀지 않도록 새 `Process`를 할당하는 등의 스케줄링이 필요한 것이다.

# Burst
`Burst`란 I/O 작업이나 CPU 작업 등 한 종류의 작업이 집중적으로 발생하는 것을 뜻하며 작업 요소에 따라 `I/O Burst`, `CPU Burst`라고 표현한다.
- `CPU Burst`는 CPU 작업 수행이며, `Process`가 Running 상태일 때 발생한다.
- `I/O Burst`는 I/O 작업 수행이며, `Process`가 Waiting 상태일 때 발생한다.

일반적으로 `Process`는 `CPU Burst` 와  `I/O Burst`가 불균형하게 일어나며 이러한 비율에 따라 `Thread`에 대한 전략을 각자 세워야 한다.

## CPU Bounded Process
`Process` 중 `CPU Burst`가 높은 `Process`를 뜻하며, 이는 CPU 작업이 많다는 뜻이기에, 최대한 많은 CPU에서 Parallel하게 `Thread`들이 작업될 수 있도록 하는 것이 바람직하다.
- CPU 코어 수와 `Thread`수의 비율을 비슷하게 설정한다.

## I/O Bounded Process
`Process` 중 `I/O Burst`가 높은 `Process`를 뜻하며, 이는 I/O 작업이 많다는 뜻이고, 동시에 해당 시점에 CPU 작업이 적다는 뜻이다.
그렇기에 I/O 작업 간 CPU가 놀지 않도록 `Thread`를 할당하는 것이 바람직하다.
- CPU 코어 수보다 `Thread` 수의 비율을 높여 I/O 작업 간 다른 `Thread`를 할당받을 수 있도록 설정한다.

