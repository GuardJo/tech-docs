> Java Thread API : Priority

# Thread 우선순위
Java의 `Thread` 에는 `priority`라는 상태 값이 존재한다.
이는 해당 `Thread` 에 대한 우선순위를 뜻하며, 우선순위가 높을 수록 단일 CPU 환경의 스케줄링 작업에서 우선적 할당 받을 수 있다.
- 단, JVM에서 우선순위에 따라 스케줄링을 하더라도 OS마다 실질적으로 `Kernel Level Thread`의 CPU 할당 정책이 상이할 수 있다.

## Priority
`Thread`의 우선순위를 표현한 정수형 데이터로, 1부터 10까지의 범위 내에서 설정 할 수 있다.
별도 설정이 없을 경우 기본 우선순위인 5가 배정되며, 숫자가 높을 수록 할당 우선순위가 높은 `Thread`이다.

**제공 상수**
`MIN_PRIORITY` : 1
`NORM_PRIORITY` : 5
`MAX_PRIORITY` : 10

### setPriority(), getPriority()

생성된 `Thread` 에 대해 `setPriority()` 메소드를 통해 `priority`를 수정할 수 있으며, 특정 `Thread`에 대해 `getPriority()` 메소드를 통해 현재 해당 `Thread`의 `priority`를 반환받을 수 있다.