> Effective Java 관련 학습 : 객체 생성과 파괴

# 1. 생성자 대신 정적 팩토리 메소드를 고려하라
Java에서 특정 객체에 대한 인스턴스를 생성하기 위해서는 기본적으로 `new` 키워드를 통해서 신규 해당 객체의 생성자를 호출하여 인스턴스를 생성하게 된다.
```java
Test test = new Test();
```
하지만 위의 경우 뿐만 아니라 Java에서는 정적 메소드 (static)를 통해 인스턴스를 구성할 수도 있다.
```java
/**  
 * The {@code Boolean} object corresponding to the primitive  
 * value {@code true}.  
 */
 public static final Boolean TRUE = new Boolean(true);  
  
/**  
 * The {@code Boolean} object corresponding to the primitive  
 * value {@code false}.  
 */
 public static final Boolean FALSE = new Boolean(false);
...
public static Boolean valueOf(boolean b) {  
    return (b ? TRUE : FALSE);  
}
```
예를 들어 위 코드는 실제 Boolean 객체에 선언되어 있는 정적 메소드이며, 해당 메소드의 응답 결과로 Boolean 타입의 객체 인스턴스가 반환된다.

그렇다면 `new` 키워드 및 생성자를 통한 방식과 정적 팩토리 메소드를 통한 방식의 차이는 무엇일까?
## 1-1. 장점
정적 팩토리 메소드 방식을 통한 인스턴스 생성 시에 아래와 같은 장점들이 존재한다.
### 1-1-1. 별도의 이름을 가질 수 있다.
생성자와는 달리 정적 팩토리 메소드는 하나의 함수이기에 작성자가 별도의 이름을 지정해둘 수 있다.
```java
public static BigInteger probablePrime(int bitLength, Random rnd) {  
    if (bitLength < 2)  
        throw new ArithmeticException("bitLength < 2");  
  
    return (bitLength < SMALL_PRIME_THRESHOLD ?  
            smallPrime(bitLength, DEFAULT_PRIME_CERTAINTY, rnd) :  
            largePrime(bitLength, DEFAULT_PRIME_CERTAINTY, rnd));  
}
```
예를 들어 Java에서 BigInteger 객체의 probablePrime 정적 메소드를 제공하는데 해당 메소드의 이름을 통해 어떠한 인스턴스가 생성되는지 유출할 수 있다.
### 1-1-2. 매번 새로운 인스턴스를 생성하지 않을 수 있다.
생성자로 인스턴스를 생성할 경우, 무조건적으로 메모리에 신규 인스턴스가 생성 및 할당된다. 이러한 작업은 상대적으로 resource 사용이 큰 요소이며, 매번 동일한 인스턴스를 생성할 경우 낭비될 수 있다.

허나 정적 팩토리 메소드로 인스턴스를 생성할 경우, 구현에 따라 이미 기생성된 인스턴스를 반환함으로써, 기존 인스턴스를 재활용할 수 있다.
- *ex) `Boolean.valueOf()`는 각각 기생성된 true, false 인스턴스를 조건에 맞춰 반환한다.*

> [!NOTE]
> **Instance-controlled Class**
> 
> 위와 같이 인스턴스의 신규 생성, 혹은 재활용 등을 제어하는 클래스를 Instance-controlled Class(인스턴스 통제 클래스) 라고 한다.
> 
> 이러한 클래스들은 인스턴스를 통제함으로써, Singleton 패턴으로 하나의 인스턴스를 유지하거나, 인스턴스 생성이 불가하도록 제어할 수 있다.

### 1-1-3. 반환 타입의 하위 타입 객체를 반환할 수 있게 된다.
생성자로 인스턴스를 생성할 경우 해당 객체에 대한 인스턴스만 생성할 수 있으나, 정적 팩토리 메소드를 통해 생성할 경우, 해당 객체를 반환하는 메소드이기에 해당 객체 뿐만 아니라 해당 객체의 하위 객체 또한 반환할 수 있다. 

이러한 장점을 통해 사용자에게 서비스를 제공하는 제공자 입장에서는 추상화된 객체 및 그에 대한 정작 팩토리 메소드 하나만으로 하위 요소들을 구별하여 제공할 수 있게 된다.

> [!NOTE]
> **인터페이스 객체에서의 정적 메소드 선언**
> 
> Java8 이전에는 인터페이스 객체에서의 정적 메소드 선언이 불가했으나, 이후 버전부터는 인터페이스 객체에도 정적 메소드 선언이 가능하다.
> 
> 허나 접근 제한자로 public하게만 구성할 수 있다.

### 1-1-4. 입력 인자에 따라 다양한 타입의 인스턴스 반환
생성자가 아닌 정적 메소드이기에 해당 타입 및 해당 타입의 하위 타입들을 로직에 따라 유연하게 반환할 수 있다.

인자의 종류나 값에 따라 여러 해당 타입 및 하위 타입으로 분기하여 구성할 수 있기에 해당 정적 팩토리 메소드를 사용하는 사용자 입장에서 보다 유연한 인스턴스 생성이 가능하다.
### 1-1-5. 정적 팩토리 메소드를 작성하는 시점에는 반환 타입에 대한 객체가 존재하지 않아도 된다.
정적 팩토리 메소드의 반환 타입이 추상 객체거나 인터페이스 객체인 경우 실제 반환해야할 구현체가 존재하지 않아도 우선은 만들어 둘 수 있다.

우선적으로 어떠한 인터페이스나 추상객체를 반환할지만 정해두면 나머지는 이후에 구현해도 된다.

이러한 특징 덕에 JPA 나 JDBC 등과 같은 Service Provide Framework 구성이 가능해진다.\

> [!NOTE]
> **Service Provide Framework**
> 
> JPA, JDBC 등과 같이 사용자에게 제공되는 일종의 프레임워크. 크게 구현체의 동작을 정의하는 `Service Interface`, 구현체를 등록하기 위한 `Provide registration API`, 사용자가 인스턴스 획득하기 위한 `Service access API` 로 이루어져 있다.
> 
> 각각의 구현체와 인터페이스가 분리된 구조를 지니기에 사용자는 여러 구현체 중 하나를 특정하여 설정함으로써 동일한 인터페이스로 다양한 구현체의 서비스를 사용할 수 있다.
> 
> - *ex) : JPA 인터페이스를 사용할 때 Hibernate 구현체를 지정해서 사용하는 행위 등*
> 
> 이러한 점이 정적 팩토리 메소드를 통한 인스턴스 생성으로 구성되었기에 가능한 기능이다.

## 1-2 단점
위의 장점들과 반대로 정적 팩토리 메소드를 사용할 경우 아래와 같은 단점들이 존재한다.
### 1-2-1. 정적 팩토리 메소드만 구성할 경우 하위 객체 생성이 불가하다.
하위 객체에 현재 객체 타입을 상속하기 위해서는 `public` 혹은  `protected` 접근 제한자를 지닌 생성자가 필요하다.
허나 정적 팩토리 메소드만을 구성할 경우 이에 해당하는 생성자들이 존재하지 않아 하위 객체를 생성할 수가 없다.
### 1-2-2. 정적 팩토리 메소드는 사용자가 찾기 어렵다.
일반 생성자 처럼 javadoc으로 명확하게 설명이 드러나지 않기에 직접 찾아서 사용해야하는 단점이 존재한다.
그렇기에 정적 팩토리 메소드 제공 객체에 대해 명확한 설명이나 주석 등을 필수적으로 달아주는 것이 좋다.
# 2. 생성자에 매개 변수가 많다면 Builder를 고려하라
정적 팩토리 메소드 방식이나 생성자 방식이나 객체 인스턴스를 생성할 때 필요한 인자가 많을 경우 가독성도 떨어지고 상대적으로 작성 간 오류가 발생할 확률이 높다.

그러한 경우 여러 패턴을 통해 이러한 문제점을 해결해볼 수 있다.

## 2-1. 점층적 생성자 패턴
인자의 개수 별로 생성자를 별도 구성하여 각각의 인자 개수에 따라 객체 인스턴스를 생성하는 방식이다.

초기에는 인자가 많은 생성자에 대해 이러한 패턴을 적용하여 각 인자 요소 별로 인스턴스를 생성하도록하였으나, 이는 각 조건 혹은 시나리오에 따라 여러 생성자 메소드를 개별적으로 생성해야되며, 이를 사용하는 사용자 입장에서도 각 인자의 뜻을 알아둬야 하는 문제가 있다. 

## 2-2. Javabeans 패턴
위의 점층적 생성자 패턴의 문제점 중 하나로 여러 인자에 대한 뜻과 그에 따라 생성되는 인스턴스 요소들을 식별하기 어려운 점 등을 해결하기 위해 생겨난 패턴이다.

주요 관점은 객체 인스턴스 생성 자체는 필수 인자만을 기반으로 생성하며, 이후 필요 정도에 따라 setter 메소드를 통해 주입하는 방식이다.

이러한 패턴 방식은 인스턴스 생성 자체는 보다 쉬워지지만, 인스턴스 생성 직후 setter 메소드 호출 전 해당 인스턴스에 대한 일관성이 깨지게 되며, setter 를 통해 필드 값을 주입함으로써 해당 객체의 멤버 필드들은 불변하게 구성할 수 없다.

## 2-3. Builder 패턴
점층적 생성자 패턴과 Javabeans 패턴 각각 단점이 뚜렷하기 때문에 이를 보완하기 위해 나온 것이 Builder 패턴이다.

점층적 생성자 패턴의 단점인 가독성과 적용 요소 식별에 대한 어려움, Javabeans 패턴의 일관성, 불변 적용 불가 문제 등을 개선하기 위해 아래와 같이 Builder 패턴을 구성할 수 있다.

```java
class Example<T> {
  	private T foo;
  	private final String bar;
  	
  	private Example(T foo, String bar) {
  		this.foo = foo;
  		this.bar = bar;
  	}
  	
  	public static <T> ExampleBuilder<T> builder() {
  		return new ExampleBuilder<T>();
  	}
  	
  	public static class ExampleBuilder<T> {
  		private T foo;
  		private String bar;
  		
  		private ExampleBuilder() {}
  		
  		public ExampleBuilder foo(T foo) {
  			this.foo = foo;
  			return this;
  		}
  		
  		public ExampleBuilder bar(String bar) {
  			this.bar = bar;
  			return this;
  		}
  		
  		@java.lang.Override public String toString() {
  			return "ExampleBuilder(foo = " + foo + ", bar = " + bar + ")";
  		}
  		
  		public Example build() {
  			return new Example(foo, bar);
  		}
  	}
}
```

객체 인스턴스 생성을 해당 객체가 아닌 해당 객체 내부의 별도 객체 (Builder) 에 해당 작업을 이관하며, Builder 객체는 setter 객체를 통해 Builder 내부 필드들에 값을 주입, 최종적으로 build() 함수 호출을 통해 원하는 타입의 인스턴스를 생성하도록 한다.

Builder 객체가 별도로 기존 객체에 필요한 필드들을 별도로 지니고 있기에 build() 메소드를 통한 실제 객체 구성이 가능하며, Builder 객체 생성 시점에 불변 값들을 구성할 수 있다.
또한 Builder에서 제공하는 setter들은 Builder 객체를 반환 타입으로 지니기에 `Fluent API` 형태로 구성할 수 있어 사용자 입장에서 가독성도 향상된다.
> [!NOTE]
> **Fluent API**
> 
> 메소드 호출이 chain 처럼 연결되어 호출되는 형태
> - ex) `builder().name("test").age(15).build();`

# 3. private 생성자나 enum 타입으로 Singleton임을 보증하라
Singleton이란 인스턴스를 오직 하나만 생성할 수 있는 객체를 뜻한다.
별도의 상태 값이 존재하지 않거나, 설계 상 유일해야할 객체 등에 Singleton 을 적용하여, 유일한 인스턴스임을 보장한다.

## 3-1. Singleton 적용 방식
Singleton 객체는 일반적으로 `private` 접근 제한자로 생성자를 구성하여, 해당 객체 내부에서 생성하는 것 외에는 인스턴스를 생성할 수 없게 제한한다.

이후 아래 두 방식으로 기생성된 인스턴스에 대한 반환을 지원한다.
### 3-1-1. public static final 필드 방식
```java
public Singleton {
	public final static Singleton INSTANCE = new Singleton();

	private Singleton() {
		// 생성자
	}
}
```
`private` 제한을 지닌 생성자를 통해 생성된 인스턴스를 `public` 하게 선언된 정적 상수 필드에 주입하여 반환한다.

위와 같이 정적 상수 필드로 제공하기 때문에 해당 객체가 Singleton 객체임을 javadoc 입장에서 명시적으로 알 수 있다. 또한 별도 메소드 호출 없이 인스턴스를 가져올 수 있다는 장점이 있다.

### 3-1-2. 정적 팩토리 메소드 방식
```java
public Singleton {
	private final Singleton INSTANCE = new Singleton();

	private Singleton() {
		// 생성자
	}

	public static Singleton getInstance() {
		return INSTANCE;
	}
}
```
유일하게 구성된 인스턴스를 필드 자체로 반환하는 것이 아닌 정적 팩토리 메소드 형태로 반환한다.

이를 통해 추후 Singleton 객체가 Singleton이 아니게 변경 될 경우 외부 호출의 변경 없이 수정할 수 있으며, 호출하는 Thread 별로 별도 인스턴스를 넘겨주게 할 수도 있다.

또한, 정적 메소드 형태이기에 Java의 `Supplier<>` 객체 형태로 함수형 프로그래밍 기법을 제공할 수 있다.
- *ex) `Singleton::getInstance`*
> [!NOTE]
> **Supplier**
> 
> Java 8에서 추가된 함수형 인터페이스 객체 중 하나로 내부적으로 해당 타입 객체를 그저 반환하는 `T get()` 메소드 하나만을 지니고 있다.
> 
> 이는 조건문 등에 따라 불필요한 연산 작업을 최소화하기 위해 사용되는 메소드로써 아래와 같은 경우 사용된다.
> ```java
> if (a == b || () -> execute()) {
> 	...
> }
> ```
> 
> 위와 같은 코드에 대해서 `a == b`
가 성립하지 않을 때에는 execute() 함수를 수행할 필요 없으며, 이러한 경우 `() -> execute()` 와 같이 Supplier 형태의 함수 인터페이스를 통해 불필요한 연산을 수행하지 않게 할 수 있다.

> [!CAUTION]
> **Singleton 객체의 직렬화**
> 
> 두 방식 모두에서 발생하는 문제로, Singleton 객체에 대해서 직렬화 시에는 다음과 같이 구성해야 한다.
> 
> 1. Singleton 객체의 모든 멤버 필드들을 `transient`로 선언하여 직렬화에 포함되지 않게 구성
> 2. readResolve() 메소드 구현
> 	```java
> 	// 역직렬화 시에 신규 인스턴스가 아닌 기존 인스턴스를 반환하도록 지정
> 	public Object readResolve() {
> 		return INSTANCE;
> 	}
> 	```
>    
> 이러한 작업이 필요한 이유는 Singleton 객체를 직렬화 한 후 역직렬화 시에 별도로 `readResolve()` 를 구현하지 않을 경우 기본적으로 신규 인스턴스를 생성하여 반환하기 때문이다.

### 3-1-3. enum 타입 방식
Singleton 객체를 반환하는 방식들 중 가장 간결한 방식으로, 원소가 하나뿐인 enum 객체를 생성하는 것이다.
```java
public enum Singleton {
	INSTANCE;
}
```

enum 타입으로 구성할 경우 직렬화 상황이나 Reflection API 를 통한 강제 인스턴스 생성 등을 모두 막아준다
- *하지만 반환하려는 Singleton 객체가 enum외의 객체를 상속한다면 사용할 수 없다.*
# 4. 인스턴스화를 막으려거든 private 생성자를 사용하라.
일부 객체는 정적 필드와 메소드들로만 이루어진 경우가 있을 수 있다.
이러한 객체들의 경우 인스턴스를 생성할 필요도 없으며, 생성자를 제공해줄 필요도 없다.
- *ex) 유틸리티성 객체*

아래는 실제 실무에서 구성되어 있는 유틸리티성 객체이다.
```java
public class ContextPath {  
    /* APU urls */  
    public static final String AGENTS = "/agents";
    ...
}
```

위의 경우 단순 ContextPath들을 정적 필드로 묶어 놓은 유틸리티성 객체이나 아래와 같이 인스턴스 생성이 가능하다.
```java
ContextPath conextPath = new ContextPath();
```

이러한 유틸리티성 객체는 인스턴스를 선언해서 사용할 일이 없으며, 혹여 다른 사용자가 이를 보고 별도의 인스턴스를 생성해서 작업을 하거나 상속해서 하위 객체를 만들게 될 수도 있다.

이러한 요소를 막기 위해서는 간단히 아래와 같이 `private`한 기본 생성자를 구성해두면 된다.
```java
public class ContextPath {  
    /* APU urls */  
    public static final String AGENTS = "/agents";
    ...
    private ContextPath() {
	    // 기본 생성자
    }
}
```
애초에 문제가 되는 요소가 생성자가 열려있기 때문인데, 이는 Java에서 별도의 생성자를 구성하지 않을 경우 `public` 한 기본생성자를 만들기 때문이다. 이를 막기 위해 `private` 제한자의 기본생성자를 구성하게 함으로써 이러한 생성자 접근을 원천적으로 막을 수 있다.

> [!NOTE]
> 본인이 사용하는 Intellij IDE에서는 이러한 유틸리티성 객체 생성 시 아래와 같은 경고를 출력해준다.
> 
> ![](images/Pasted%20image%2020240108161334.png)

# 5. 자원을 직접 명시하지 말고 Dependency Injection을 사용하라
하나의 객체는 작업 수행을 위해 여러 다른 객체의 인스턴스들을 사용하게 된다. 이 때 이러한 인스턴스들을 수행할 객체 내부에서 생성할 경우 여러 문제가 발생할 수 있다.

```java
public class MainClass {
	private final Item item = new Item();

	public void checkItem() {
		item.check();
	}
}
```
예를 들어 위와 같이 Item 이라는 객체 의존을 지닌 MainClass 객체가 있을 경우, 해당 객체는 내부에 생성된 인스턴스에 강하게 묶여 있는 형태가 된다.

이는 만약 다른 Item 인스턴스나 하위 타입의 인스턴스가 필요할 경우, 별도의 수정 작업 혹은 개선이 필요하게 된다.

이러한 상황에 대응하며, 의존 객체와의 관계성을 낮추기 위해 의존 객채 주입(Dependency Injection) 패턴이 존재한다.
```java
public class MainClass {
	private final Item item;

	public MainClass(Item item) {
		this.item = item;
	}

	public void checkItem() {
		item.check();
	}
}
```
위와 같이 의존성이 있는 객체 인스턴스(Item)을 생성자를 통해 외부에서 주입받음으로써, MainClass의 수정 없이 Item 타입의 다른 인스턴스 혹은 Item 하위 타입의 인스턴스 등 다양한 인스턴스들을 주입받아 사용할 수 있게 된다.

# 6. 불필요한 객체 생성을 피하라
반드시 객체 인스턴스가 개별적으로 구성되어야할 필요가 없다면, 하나의 인스턴스를 재사용하는 것이 성능 및 가독성 측면에서 좋다.

예를 들어 Strng 타입 객체 인스턴스는 두 가지 방법으로 초기화 할 수 있다.
```java
String str1 = new String("Test");
String str2 = "Test2";
```
첫번째의 경우, 매번 String 타입의 인스턴스를 신규로 생성한다. 허나 두번째의 경우 JVM 내부적으로 `"Test2` 와 동일한 값들은 하나의 인스턴스로 취급하기에 하나의 인스턴스만 생성된 후 재사용된다.

인스턴스를 생성하는 비용이 상대적으로 비싼 경우, 이러한 객체 인스턴스를 매번 생성하는 것은 자원 관리 측면에서도 좋지 않으며, 더욱이 하나의 인스턴스만으로 유지가 가능하다면 최초 생성된 인스턴스를 재사용하는 것이 OOP 측면에서도 지향하는 요소이다.
# 7. 다 쓴 객체 참조를 해제하라
C/CPP 와 다르게 Java에서는 더 이상 쓰이지 않는 객체들은 `Garbage Collector`에서 메모리를 알아서 해제해준다.
허나 이러한 상황에 익숙해져 할당된 메모리를 신경쓰지 않으면 일부 상황에서는 GC가 일어나지 않아 메모리 누수가 발생할 수 있다.

```java
public class TestStack {
	private Object[] objects;
	private int size = 0;
	...
	public Object pop() {
		if (size == 0) {
			// exception
		}
		return objects[--size];
	}
	...
}
```

만약 위와 같이 직접 Stack을 구현했을 경우, 어느 정도 Objects[] 에 데이터가 쌓인 상태에서 pop() 함수 호출을 통해 index 위치를 조정할 경우, 해당 index보다 상위에 남아있는 데이터들은 여전히 참조되어 있어 메모리 해제가 되지 않는다.

위와 같이 메모리에 직접 접근하게 되는 배열 타입과 같은 객체들에서는 이러한 메모리 해제를 신경써야 하며, 아래와 같이 메모리를 직접 해제해주어야 한다.
```java
public Object pop() {
	...
	Object result = objects[--size];
	objects[size] = null;
	return result
}
```

Java에서 특정 인스턴스의 메모리를 해제하려면 해당 인스턴스를 null로 초기화 해주면 된다.

> [!NOTE]
> **WeakReference**
> 
> Java에서는 `WeakReference`라는 약한 참조 기반의 객체 및 기능을 제공한다.
> 아래와 같이 사용할 수 있으며, 이렇게 선언된 객체는 원본 인스턴스의 메모리가 해제 되었을 경우, 해당 인스턴스를 참조하던 다른 인스턴스들도 적재된 메모리를 제거한다.
> 
> ```java
> Integet number = 1;
> WeakReference<Integer\> ref = new WeakReference(number);
> ```

# 8. finalizer와 cleaner 사용을 피하라
Java에서는 객체에 대한 소멸자로 `finalizer`와 `cleaner`를 제공한다.
허나 C++ 의 소멸자와 다르게 Java의 소멸자들은 어느 시점에 호출될 지 작성자 입장에서는 알 수 가 없다. 

일반적으로 Java에서 객체 인스턴스에 대한 생명주기는 GC를 통한 메모리 관리 하에 이루어지며, `finalizer`와 `cleaner`는 GC에 의해 객체가 소멸 될 때에나 실행되며, 이는 명확하게 어느 시점인지 정해진 것이 아니라, 해당 환경의 메모리 상황에 따라 다르다.
- `System` 객체의 `gc()`나 `runFinalization()` 메소드가 있으나 반드시 소멸자를 실행시켜준다고 보장 할 수는 없다.

또한 악의적인 기능을 담은 `finalizer`를 구현하여 상위 객체의 직렬화 과정에서 `readResolve()`등의 메소드를 구현하여 직렬화 간 예외 발생 시 하위 요소의 `finalizer`를 호출하여, 악의적인 기능을 실행하게 할 수도 있다.

그렇기에 Java에서는 `finalizer`나 `cleaner` 대신 `AutoCloseable` 에 대한 구현체를 구성하여, 인스턴스 할당 후 작업이 끝나면 `close()`를 호출하여 안전하게 자원을 해제해주는 것이 좋다.
