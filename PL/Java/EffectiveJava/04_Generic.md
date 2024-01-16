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


# 2. 비검사 경고를 제거하라
`Generic`을 사용하면서 형변환 등의 이슈로 컴파일러가 여러 경고를 출력할 수 있다. 이러한 경고를 비검사 경고라고 하며 최대한 이러한 경고를 수용하여 수정함으로써 `ClassCastException`을 방지할 수 있다.

허나, 간혹 컴파일러가 내보내는 비검사 경고 중 아래와 같이 실제로는 문제가 발생하지 않는 경우도 존재한다. 그런 경우, `@SuppressWarnings("unchecked")` 어노테이션을 통해 관련된 비검사 경고를 무시할 수 있다.
- *단, 이러한 비검사 경고 무시는 명확하게 해당 경고에 대한 오류가 발생하지 않을 때에만 제외하며, 제외 시에 관련 부분에 대해 주석으로 명시해주는 것이 좋음*
- *또한, `@SuppressWarning`의 적용 범위를 최대한 작게 잡아주어야 의도치 않은 다른 치명적인 경고들을 무시하지 않도록 할 수 있음*

# 3. 배열보다는 리스트를 사용하라
Java에서의 배열은 `공변` 속성을 지니고 있고, 리스트와 같은 `Generic`은 `불공변` 속성을 지니고 있다.
- 공변 : 어떠한 두 타입이 서로 상하 관계이면, 그에 대한 배열도 상하 관계인 것
- 불공변 : 어떠한 두 타입의 관계와 상관 없이 그에 대한 `Generic`은 서로 관계가 없는 것

예를 들어 배열의 경우 아래와 같이 구현할 경우, 컴파일에는 문제가 없다
```java
Object[] objects = new Long[1];
objects[0] = "test";
```
배열의 경우, 위와 같은 코드는 컴파일 시점에는 문제가 없지만 런타임에 Long 타입 배열에 String 타입 값을 추가하려 하기에 Exception 이 발생한다.

그에 반해 `Generic` 중 하나 인 List의 경우 아래와 같이 구현할 경우 컴파일 시점에 오류가 발생한다.
```java
List<Object> list = new ArrayList<Long>();
list.add("test");
```
위 코드는 Object 타입의 List를 Long 타입의 List로 초기화 하려 할 때 이미 오류가 발생한다.
- *불공변 속성에 의해 두 List 객체는 아예 다른 타입이기 때문*

위와 같이 Java에서 배열과 `Generic`은 서로 반대되는 속성을 지니고 있으며, 개발자 입장에서는 두 요소 중 하나로 구현해야할 경우 `Generic` 을 사용하는 것이 형변환, 에러 처리 등에 보다 명확하게 컴파일 시점에 파악할 수 있기에 `Generic`으로 구성하는 것이 좋다.

> [!NOTE]
> Java에서 배열과 List 등의 제네릭 객체는 서로 반대되는 속성을 지니고 있기에 제네릭 객체의 타입 매개변수로 배열은 제공하지 않는다.

# 4. 이왕이면 제네릭 타입으로 만들라
Java에서 지원하는 `Generic`은 타입 매개변수에 대한 자동 형변환 및 컴파일 시점 비검사 경고 등을 제공하기에 아래와 같은 코드는 `Generic` 으로 개선하는 것이 좋다.

```java
public class CustomStack {  
    private final static int DEFAULT_CAPACITY = 20;  
    private Object[] elements;  
    private int size = 0;  
  
    public CustomStack() {  
       elements = new Object[DEFAULT_CAPACITY];  
    }  
  
    public void push(Object o) {  
       ensureCapacity();  
       elements[size++] = o;  
    }  
  
    public Object pop() {  
       if (size == 0) {  
          throw new EmptyStackException();  
       }  
       Object result = elements[size--];  
       elements[size] = null;  
  
       return result;  
    }  
  
    private void ensureCapacity() {  
       if (elements.length == size) {  
          elements = Arrays.copyOf(elements, 2 * size + 1);  
       }  
    }  
}
```

위 코드의 경우 작동에는 문제가 없으나, pop() 등의 메소드를 통해 원소를 꺼내온 경우 꺼낸 원소를 사용자가 별도의 형변환을 거쳐야 하는 불편함이 존재한다.

그렇기에 위의 코드를  `Generic`으로 개선하기 위해서는 우선 아래와 같이 Object 타입을 타입 매개변수로 변경해야 한다.
```java
public class CustomStack<E> {  
    private final static int DEFAULT_CAPACITY = 20;  
    private E[] elements;  
    private int size = 0;  
  
    public CustomStack() {  
       elements = new E[DEFAULT_CAPACITY];  
    }  
  
    public void push(E o) {  
       ensureCapacity();  
       elements[size++] = o;  
    }  
  
    public E pop() {  
       if (size == 0) {  
          throw new EmptyStackException();  
       }  
       E result = elements[size--];  
       elements[size] = null;  
  
       return result;  
    }  
  
    private void ensureCapacity() {  
       if (elements.length == size) {  
          elements = Arrays.copyOf(elements, 2 * size + 1);  
       }  
    }  
}
```

class 명 부분에 타입 매개변수를 추가하였으며, 내부 배열의 타입을 타입 매개변수 E 로 수정하였다.
하지만 위 코드는 정상적으로 수헹되지 않는다.  앞선 내용에 따라 배열과 `Generic`은 서로 상반된 속성을 지니고 있기에 배열의 타입을 `Generic`으로 초기화 할 수가 없다.

그렇기에 보통 이러한 경우 아래 두 가지 경우로 구성한다.

**(1) Object 배열 생성 후 E 로 형변환**
```java
@SuppressWarnings("unchecked")  
public CustomStack() {  
    elements = (E[])new Object[DEFAULT_CAPACITY];  
}
```
위와 같이 우선 Object 타입으로 배열 생성 후 이를 E 로 형변환 하는 방법이 있다.
비록 컴파일러가 해당 요소에 대해 비검사 경고를 주긴 하나, 개발자 입장에서 해당 배열을 private 으로 선언 후 해당 배열에 주입되는 push() 메소드에 인자로 E를 받고 있기에 이 배열에는 E 타입만 주입된다는 것을 알 수 있다.
- *타입에 대한 안정성이 확보되기에 해당 비검사 경고를 작은 범위에서 제외해줌*

**(2) Object 배열 생성, 반환 타입을 E로 형변환**
```java
public E pop() {  
    if (size == 0) {  
       throw new EmptyStackException();  
    }  
    @SuppressWarnings("unchecked") E result = (E)elements[size--];  
    elements[size] = null;  
    
    return result;  
}
```

위와 같이 기본 배열은 Object 타입으로 두고 내부 원소 반환 시에만 E 로 형변환할 수도 있다. 앞선 방법과 마찬가지로 push() 함수가 이미 E 타입의 객체만을 가져오기에 해당 형변환은 안전함을 확인할 수 있으며, 그에 따라 경고를 제외할 수 있다.

> [!NOTE]
> 결국 어떠한 신규 객체를 구현할 때에는 형변환을 컴파일러 입장에서 지원하도록 `Generic`으로 구현하는 것이 더 안전하고 쓰기 편하다.

# 5. 이왕이면 제네릭 메소드로 만들라
앞선 제네릭 타입과 마찬가지로 사용자 입장에서 명시적인 형변환 등이 필요한 메소드의 경우, 이를 `Generic` 형태의 메소드로 개선하여 사용하는 것이 사용자 입장에서 보다 편리하며, 개발자 입장에서도 보다 안정적인 형변환을 제공할 수 있도록 해준다

예를 들어 아래와 같이 Raw 타입의 Set을 반환하는 메소드의 경우 반환 결과를 사용자가 직접 형변화해주어야 한다.
```java
public Set union(Set s1, Set s2) {
	Set result = new Hashset(s1);
	result.addAll(s2);

	return result;
}
```

위와 같이 인자나 반환 타입 모두 Raw 타입이기에 이에 대한 형변환은 오롯이 사용자의 몫이며, 개발자 또한 해당 메소드 구현 간에 컴파일 시점에서 어떠한 오류가 발생할 지 알기 어렵다.

하지만 아래와 같이 `Generic`으로 개선할 경우 위에 대해 대부분의 문제를 개선할 수 있다.
```java
public Set<T> union(Set<T> s1, Set<T> s2) {
	Set<T> result = new Hashset(s1);
	result.addAll(s2);
	return result;
}
```

위와 같이 `Generic` 으로 선언하였기에 입력받는 인자에 대한 타입 검증, 반환 타입 등에 대한 지원등을 Java에 일임할 수 있다.

# 6. 한정적 와일드카드를 사용해 API 유언성을 높여라
Java에서 `Generic` 타입의 타입 매개변수는 불공변한 속성을 지니고 있다.
`List<String>`, `List<Object>`를 예로 보면 이 둘의 타입 매개변수만 보았을 때에는 `String` 타입이 `Object` 타입의 하위 타입이지만, `List` 로써 보면 서로는 별개의 객체이다.
- *`List<String>`에는 String 포함 하위타입만 주입할 수 있으나, `List<Object>`에는 Object이하 모든 하위 타입을 주입할 수 있기에, `List<String>`이 모든 일을 대체할 수 없어, 하위 타입이 아닌 것이 맞음 ([리스코프 치환 원칙](../../OOP.md))*

하지만 이러한 불공변 속성 때문에 아래와 같은 시나리오에서 문제가 발생할 수 있다.
```java
public Stack<E> {
	...
	public void pushAll(Interable<E> src) {
		for (E e : src) {
			push(e);
		}
	}
}
...
Stack<Number> numberStack = new Stack<>();
Interable<Integer> integers = ...;
numberStack.pushAll(integers); // Error!
```

위와 같이 `Generic`한 Stack이 구현되어 있을 경우, 타입 매개변수가 `Number`인 Stack에 `Number`의 하위 타입인 `Integer`를 넣게 되면 오류가 발생한다.
- *사용자 입장에서는 `Integer`가 `Number`의 하위 타입이니 주입되어야 한다고 생각함*

이러한 문제가 발생할 수 있기에 `Generic` 기반으로 API를 구현할 때에는 아래와 같이 특정 타입 매개변수 뿐 아니라 조금 더 포괄적으로 타입 매개변수를 관리해야 한다.
```java
public void pushAll(Iterable<? extends E> src) {
	...
} 
```

위와 같이 `? extends E` 등을 한정적 타입 매개변수라 하며, 이는 E 라는 타입 매개변수의 하위 타입까지 입력으로 받아들일 수 있다는 뜻이다.
- *위와 같이 수정할 경우 E (`Numnber`) 일 때, ? (`Integer`) 가 `Number`의 하위타입이 맞기에 사용자가 생각한대로 동작할 수 있게 됨*

위와 같은 경우는 입력으로 주어진 인자를 특정 객체 내 주입 하는 등의 생성자 (Producer) 역할이었으며, 아래와 같이 입력 인자에 작업을 수행하여 소비하는 소비자 (consumer) 일 때는 별도의 한정적 타입 매개 변수를 사용해야 한다.
```java
public void popAll(Collection<E> dst) {
	while (!isEmpty()) {
		dst.add(pop());
	}
}
...
Stack<Number> numberStack = new Stack<>();
Collection<Object> objects = ...;
numberStack.popAll(objects);
```

위의 경우 `Object` 는 `Number` 타입보다 상위 객체이며, 그렇기에 `Number` 타입의 Stack에서 pop() 수행이 자연스러워 보이지만, 마찬가지로 `Generic`의 불공변 속성으로 인해 제대로 수행되지 않는다.

popAll() 과 같은 소비자 (Consumer) 인 인자를 받을 때에는 생성자 (Producer) 와는 반대로 인자의 타입이 타입 매개변수의 상위 타입이어야 한다.

```java
public void popAll(Collection<? super E> dst) {
	...
}
```

위와 같이 `? super E` 처럼 해당 타입 매개변수의 상위 타입 임을 한정적 타입 매개변수로 지정하여 보다 유연하게 값들을 입력 받을 수 있게 된다.
- *모든 타입은 자기 자신의 상위 타입임*

> [!NOTE]
> **PESC 공식**
> 
> PESC : Producer-Extends, Consumer-Super 의 약자로, 모든 생성자는 해당 타입 매개변수의 하위 타입을 포함해야 하며, 모든 소비자는 해당 타입 매개변수의 상위 타입을 포함해야 한다.

