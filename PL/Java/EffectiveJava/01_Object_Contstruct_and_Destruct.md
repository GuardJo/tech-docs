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

> [!INFO]
> **Instance-controlled Class**
> 
> 위와 같이 인스턴스의 신규 생성, 혹은 재활용 등을 제어하는 클래스를 Instance-controlled Class(인스턴스 통제 클래스) 라고 한다.
> 
> 이러한 클래스들은 인스턴스를 통제함으로써, Singleton 패턴으로 하나의 인스턴스를 유지하거나, 인스턴스 생성이 불가하도록 제어할 수 있다.

### 1-1-3. 반환 타입의 하위 타입 객체를 반환할 수 있게 된다.
생성자로 인스턴스를 생성할 경우 해당 객체에 대한 인스턴스만 생성할 수 있으나, 정적 팩토리 메소드를 통해 생성할 경우, 해당 객체를 반환하는 메소드이기에 해당 객체 뿐만 아니라 해당 객체의 하위 객체 또한 반환할 수 있다. 

이러한 장점을 통해 사용자에게 서비스를 제공하는 제공자 입장에서는 추상화된 객체 및 그에 대한 정작 팩토리 메소드 하나만으로 하위 요소들을 구별하여 제공할 수 있게 된다.

> [!INFO]
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

> [!INFO]
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
> [!INFO]
> **Fluent API**
> 
> 메소드 호출이 chain 처럼 연결되어 호출되는 형태
> - ex) `builder().name("test").age(15).build();`

