> 동기화 기초 : Safety Thread

# Safety Thread
동시성 이슈를 고려하여 객체나 소스를 구현하여 `Multi Thread` 환경에서 관련 문제들이 발생하지 않도록 하는 것을 `Thread Safe`, `Thread`에 안전하다고 표현한다.

# Thread Safe 구조
## Critical Section 동기화
`Critical Section` 에 접근하는 `Thread`를 동기화하여 관리함으로써 동시성 이슈를 방지한다.
## 동기화 도구 사용
`Semaphore`, `CAS`, `Atomic 변수` 등을 사용하여 동기화 지원을 받는다.
## Thread의 Stack에 한정된 자원
객체 구성 시 `Thread` 별로 독립적이도록 지역변수들을 사용하여 Stack 영역에서 처리할 수 있도록 한다.
- *Stack영역은 `Thread` 별로 독립적이기 때문*

### 지역 변수 참조
지역 변수로 선언된 데이터를 참조하여 사용할 경우, 크게 기본 타입 변수와 객체 참조 타입 변수로 구분하여 `Thread Safe` 여부를 확인한다.

**기본 타입 변수**
```java
public void execute(int number) {
	...
}
```
위의 메소드를 `Multi Thread` 환경에서 호출 시 인자로 주어지는 변수가 `Integer` 기본 타입이기에 Stack 영역에 할당되어 `Thread-safe` 하다.

**객체 참조 변수**
```java
// Thread Safe
public void execute(String name) {
	Person p = new Person(name)
}

// Thread Non Safe
public void execute(Person person) {
	...
}
```

위의 메소드들과 같이 `Multi Thread` 환경에서 특정 객체에 대한 접근이 존재할 경우, 메소드 내에서 `new` 키워드를 통해 신규 인스턴스로 생성되는 객체는 `Thread Safe` 하다.

허나 외부에서 이미 생성된 객체 인스턴스 자체를 인자로 받아올 경우에는 `Thread Safe` 하지 않다.
## ThreadLocal 사용
`Thread` 마다 지닌 `ThreadLocal`을 사용하여 자원 간 독립성을 유지한다.
## 불변 객체 사용
불변 객체를 사용하여 애초에 수정이 불가하도록 한다.
