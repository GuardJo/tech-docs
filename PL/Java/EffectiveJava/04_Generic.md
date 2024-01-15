> Effective Java 관련 학습 : 제네릭

# 1. Raw 타입은 사용하지 말라
Java 5부터 `Generic Type`을 지원하기 시작했다. 클래스나 인터페이스에 타입 매개변수가 쓰이면, 이를 `Generic Class`, `Generic Interface`라고 부른다.
- ex) : `List<E>`, `Set<E>` 등

위와 같은 `Generic Type`은 실제 사용 시에 `<>` 기호를 통해 매개변수화 타입을 정의하여 해당 객체가 특정 타입을 기반으로 구성했다고 명시한다.

Java 컴파일러 입장에서는 `Generic` 객체들은 `Generic Type` 지정을 통해 어떠한 타입을 받아들일지 알게 되므로, 실행을 하지 않더라도 컴파일 시점에 어떠한 문제가 발생하는지 등을 알 수 있으며, 형변환 또한 자동으로 진행해준다.

이러한 `Generic` 객체의 장점에도 불구하고 Java에서는 굳이 `Raw`타입이라는 것을 지원한다.
`Raw` 타입이란 `Generic Type`에서 타입 매개변수를 사용하지 않는 타입을 뜻한다.
- ex) : `List`, `Set`

위와 같은 Raw 타입 객체들은 컴파일러 입장에서 어떠한 매개변수가 올지 모르기에 `Generic`에서 제공하던 기능 등을 수행하지 않는다.
즉, 컴파일 시점에 관련된 오류를 검출 할 수 없으며, 형변환 또한 지원하지 않는다.

```java
List list = new ArrayList<>();
String str = "test";
int num = 333;

list.add(str);
list.add(nul);
...
for (String s : list) {
	String element = s; // Runtime Error
}
```

예를 들어 위와 같이 `Raw`타입으로 초기화한 List 객체의 경우 해당 객체는 `Generic` 의 효과를 보지 못하여, 컴파일까진 정상적인것처럼 진행되나, 타입을 구분하지 못하고 받아들이기에 실제 데이터 조회 시에 에러가 발생한다.
- *list가 타입 매개변수가 존재하지 않는 Raw 타입이기에 데이터 추가 간에 어떠한 오류도 검출하지 못하게 됨*

하지만 아래와 같이 `Generic Type`에 타입 매개변수를 명시적으로 추가하여 사용할 경우에는 이러한 문제를 사전에 검출할 수 있다.

```java
List<String> list = new ArrayList<>();
String str = "test";
int num = 333;

list.add(str);
list.add(num); // Compile Error
```

위와 같이 타입 매개변수를 String으로 명시하여 해당 `Generic class`는 String 타입만 받아들일 수 있게 컴파일러에게 명시함으로써 컴파일 시점에 문제 요소를 식별할 수 있게 된다.

> [!NOTE]
> **Generic의 Raw 타입의 존재 이유**
> 
>  그렇다면 단점 투성이인 `Raw`타입이 존재하는 이유가 무엇인가 하면, Java 진영에서 `Generic`을 받아들이는 동안 이미 그 이전에 구현된 모든 객체 및 라이브러리들은 `Generic`이 없었기에 `Raw` 타입을 기반으로 구성되었으며, 이를 직접 형변환 하여 사용하였다.
>  그로 인해 Java에서도 하위 호환성을 위해 어쩔 수 없이 `Raw` 타입을 사용할 수 있도록 제공하고 있는 것이다.

