> 운영체제 기초 : 사용자 수준 Thread 와 커널 수준 Thread

# Thread 종류
OS에서는 크게 두 종류의 `Thread`가 있으며, 각 관리 주체에 따라 아래 두 종류로 이루어져 있다.

## User Level Thread
`Thread`를 구분하는 종류 중 하나로 사용자 영역에서 `Thread` 라이브러리를 통해 생성 및 관리되는 `Thread`를 뜻한다.
- OS는 `User Level Thread`를 알지 못하며 실제로 CPU에 할당되는 요소가 아니다.
## Kernel Level Thread
커널 영역에서 관리되는 `Thread`이며, 커널은 이러한 `Kernel Level Thread` 에 대한 모든 정보를 지니고 있다.
- CPU는 오직 `Kernel Level Thread`만을 할당 받아 작업을 수행한다.

# Multi Thread Model
CPU는 오직 `Kernel Level Thread`만을 할당 받기에 `User Level Thread`와 `Kernel Level Thread` 가 매핑되어야 `User Level Thread`의 작업을 수행할 수 있게 된다.

이에 따라 아래 세 종류의 매핑 타입이 존재한다.

## ManyToOne Thread Mapping
다수의 `User Level Thread`가 하나의 `Kernel Level Thread` 에 매핑되는 형태이다.

![](images/Pasted%20image%2020240409165936.png)

`Kernel Level Thread`에서는 별도로 `User Level Thread`에 대한 관리를 하지 않으며, 사용자 영역에서만 `User Level Thread`들을 관리하여 `Kernel Level Thread`에 매핑되도록 한다.
- 커널의 개입이 없기에 `User Level Thread` 들 끼리의 `Context Switching` 비용이 상대적으로 적다.
- 다수의 `User Level Thread`가 오직 하나의 `Kernel Level Thread`에 매핑되기에 `Process` 별로 병렬 수행이 불가하다.

## OneToOne Thread Mapping
하나의 `User Level Thread`가 하나의 `Kernel Level Thread` 에 매핑되는 형태이다.

![](images/Pasted%20image%2020240409165952.png)

`Kernel Level Thread`에 일대일로 매핑되기에 커널 수준에서 `Thread` 들에 대한 관리가 이루어진다.
- `Kernel Level Thread`는 `User Level Thread`에 비해 생성 비용이 크기에 결과적으로 일대일로 매핑된 `User Level Thread`의 생성 비용도 상대적으로 크다.
	- *그렇기에 이러한 매핑 모델에서는 Thread Pool을 생성하여 `Thread`들을 관리한다.*
- `Thread` 들이 일대일로 매핑되었기에 CPU 단위의 병렬 처리가 가능하다.

## ManyToMany Thread Mapping
다수의 `User Level Thread`가 그보다는 적은 수의 `Kernel Level Thread`에 매핑되는 형태이다.

![](images/Pasted%20image%2020240409170010.png)

각 `Kernel Level Thread`는 1개 이상의 `User Level Thread`에 매핑되며, 이에 따라 다대일, 일대일 모델에 대한 단점이 어느정도 해소된다.
- `User Level Thread`를 필요한 만큼 생성 할 수 있으며, `Kernel Level Thread` 에서 병렬 처리가 가능하다.
- `User Level Thread`가 System Call을 발생시켰을 경우, 커널이 다른 `Thread`의 수행을 스케줄할 수 있다.

