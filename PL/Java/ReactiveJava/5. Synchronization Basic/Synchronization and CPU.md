> 동기화 기초 : 동기화와 CPU 관계

# Synchronization
동기화 작업이라 하며, `Multi Thread` 환경에서 동시 접근하는 자원에 대한 불일치 문제를 해결하여 자원에 대한 일관성을 유지하기 위한 작업이다.
- Java의  `synchronized` 키워드
- Java의 `Atomic*` 객체들
- Java의 `trasient` 키워드 등

## CPU 연산 간 자원 접근 방식
`synchronization` 이 필요한 근본 원인 중 하나는 CPU가 수행하는 작업의 단위는 실제 기계어 명령어에 대해 각각 하나씩을 수행하며, 각각의 명령어 사이에는 `Context Switching`에 의해 다른 명령이 수행될 수 있기 때문이다.

### 명령어 수행 예시

| 명령 수행 흐름       |
| -------------- |
| LOAD R1, data  |
| ADD R1, 1      |
| STORE R1, data |
한 `Thread`의 작업이 위와 같을 경우 각각의 명령 수행 간에 다른 `Thread`의 작업이 수행될 수 있으며, 다른 작업 시점에 data 자원에 대한 읽기/쓰기 작업이 수행 될 경우 기존 자원에 대한 일관성이 깨지게 된다.

그렇기에 이러한 공유 자원 사용 시 이슈를 최소화하기 위해 `Synchronization`을 통해 일관성을 유지하도록 한다.