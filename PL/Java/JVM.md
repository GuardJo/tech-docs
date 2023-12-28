> JVM 에 대한 내용 정리
# 1. JVM이란?
Java Virtual Machine의 약자로, Java 언어의 주요 특징 중 하나인 OS에 구애받지 않는다는 특징을 살려 OS 위에 가상머신 형태로 구성되어 Java에서 컴파일된 바이트코드를 읽어 실행하도록 해준다.

즉, 어떠한 OS에서도 Java 기반의 언어를 수행할 수 있도록 OS 위에 가상머신 형태로 구동되는 요소를 뜻한다.
## 1-1. Java의 컴파일 방식
Java에서 기본적인 .java 파일은 사람이 읽을 수 있도록 추상화된 높은 레벨의 언어로 이를 아래와 같은 순서로 OS가 읽어들일 수 있도록 변환한다.

![](images/Pasted%20image%2020231219011013.png)

1. .java 파일을 javac를 통해 컴파일한다.
2. 그에 따른 결과로 바이트코드로 이루어진 .class 파일이 생성된다
3. 생성된 .class 파일을 JVM의 ClassLoader 올려 JVM이 읽을 수 있도록 한다.
4. JVM은 ClassLoader에 존재하는 .class 파일들을 읽어 실제 OS가 읽을 수 있는 기계어로 번역한다.
5. 번역된 기계어가 수행된다.
# 2. JVM 구조
기본적으로 JVM 은 아래의 요소들로 구성되어 있다.

![](images/Pasted%20image%2020231219012838.png)

## 2-1. Class Loader
컴파일된 .class 파일들을 load 하고 link 하는 등의 작업을 수행한다.
### 2-1-1. Class Loader 특징

![](images/Pasted%20image%2020231219013552.png)

**계층 구조**
- ClassLoader는 최상위 ClassLoader인 Bootstrap Class Loader로부터 하위 ClassLoader들 까지 계층 구조를 이룬다.
	- `Bootstrap Class Loader` : JAVA_HOME/lib 내 존재하는 Java API를 제공
	- `Extension Class Loader` : JAVA_HOME/lib/ext 내 존재하는 class들을 제공
	- `Application Class Loader` : -classspath의 class들을 제공
	- `User Defined Class Loader` : 사용자가 직접 코드 상에서 생성해서 사용하는 ClassLoader

**위임 모델**
- 계층 구조를 기반으로 ClassLoader 들끼리 load 작업을 위임할 수 있다. 
	- class를 load할 때 상위 ClassLoader에 해당 class가 존재하면 상위 ClassLoader에 load 작업을 위임하며, 없을 경우엔 현재 ClassLoader에서 load 함

**가시성 제한**
- 하위 ClassLoader는 상위 ClassLoader를 찾을 수 있지만 반대로 상위 ClassLoader에서는 하위 ClassLoader를 찾을 수 없다.

**Unload 불가**
- ClassLoader는 class를 load할 순 있지만 unload할 수는 없다.
## 2-1-2. Loading
ClassLoader가 .class 파일들을 읽고 번역된 이진 데이터를 Method 영역에 저장한다.

**Method 영역에 저장되는 요소**
- FGCN (Full Qualified Class Name)
- Class, Interface, Enum 객체
- 메소드와 변

Loading 작업이 끝나면 Loading 된 타입의 Class 객체를 생성하여 Heap 영역에 저장한다.

### 2-1-3. Linking
Linking 작업은 아래 세 단계로 진행된다.

**Verify**
- 읽어들인 Class가 JVM 명세대로 구성된 요소인지 검증한다.

**Perpare**
- Class가 필요로 하는 메모리를 할당한다.

**Resolve**
- Class의 Constant Pool 내에 존재하는 모든 Symbolic 참조들을 Direct 참조로 변경한다.

### 2-1-4. Initialization
Loading, Linking 과정을 거친 Class들을 적절한 값으로 초기화한다.
- *이 때 e 변수들을 초기화한다.*
## 2-2. Execution (Engine)
ClassLoader에 의해 JVM 내 Runtime Data Area 에 적재된 바이트 코드들을 실행한다.
이 때 적재된 바이트 코드를 실제 기계가 인식할 수 있는 기계어로 변환하여 작업을 수행한다.

기계어에 대한 수행은 아래 두 가지의 요소를 활용한다.

### 2-2-1. Interpreter
바이트코드 명령어를 한 줄 씩 읽어서 해석하고 곧바로 실행한다.
JVM 내의 바이트코드들은 기본적으로 Interpreter 방식으로 명령을 수행한다.

허나, 같은 메소드라도 여러번 호출된다면 매번 해석을 진행하기에 전체적인 속도는 느리다.

### 2-2-2. JIT Compiler
Interpreter의 단점을 보완하기 위해 도입된 것으로써, 바이트코드 전체를 컴파일 하여 Native Code 로 변경한다.
이후 해당 바이트코드에서 실행된 메소드들은 이미 Natvice Code로 변경되어 있기에 별도의 Interpreter의 작업 없이 코드를 실행 할 수 있다. 

코드를 한 줄 씩 Interpreting 하는 것이 아니기에 Interpreter보다 빠르지만, 바이트코드 전체를 컴파일 하는데에 비용이 소모되므로, JVM은 모든 코드를 JIT 컴파일러로 컴파일하지 않고 Interpreter를 기반으로 작업을 수행하다가 일정 기준이 넘어가면 JIT 컴파일러로 수행한다.
>[!INFO]
>**Natvie Code**
>
>Java 입장에서 구현체에 가까운 C나 C++, 어셈블리어 등으로 구성된 코드를 뜻한다.

### 2-2-3. GC : Garbage Collector
JVM의 주요 장점 중 하나로써, 해당 요소를 통해 Heap 메모리 영역에서 더이상 사용하지 않는 인스턴스들의 메모리 할당을 자동으로 해제해준다.

Garbage Collector는 정해진 규칙에 따라 메모리 내부를 정리하는데, 정리하는 구역 및 크기에 따라 Minor GC와 Major GC로 나뉜다.

> [!INFO]
> **STOP THE WORLD**
> 
> Major GC가 발생할 경우 JVM 내 모든 Thread는 Major GC 작업이 끝날 때까지 동작을 멈추게 되며, 이를 Stop The World 라고 표현한다.
> 
> Garbage Collector는 이러한 Major GC의 주기 및 소요 시간 등에 따라 여러 알고리즘으로 나뉜다.

## 2-3. Runtime Data Area
JVM 위에서 기동되는 프로그램을 실행하기 위해 OS로부터 할당 받은 메모리 공간이다.

![](images/Pasted%20image%2020231219023458.png)

### 2-3-1. PC (Register)
개별 Thread가 시작될 때 생성되며 Thread마다 1개씩 존재한다.
해당하는 Thread가 어떠한 명령을 수행했을 때의 지점들을 기록하여 현재 수행중인 JVM 명령의 주소를 지닌다.
### 2-3-2. JVM Stack
메소드 내에서 실행되는 지역 변수나 임시 데이터, 인자, 반환 값 등이 저장되는 메모리 공간이다.
각각의 메소드 호출마다 스택 프레임이 생성되어 해당 메소드 내의 지역변수 등을 저장하며, 메소드 작업이 끝나고 반환 될 때 스택 프레임도 제거된다.
### 2-3-3. Native Method Stack
Java 를 기반으로 컴파일되어 생성된 바이트 코드가 아닌 실제 별도로 실행 가능한 기계어로 작성된 프로그램을 실행시키는 메모리 영역이다.
주로 C나 C++ 등의 코드를 수행하기 위한 영역이다. (JNI)
### 2-3-4. Heap
JVM 위의 모든 Thread와 공유하는 메모리 영역으로, new 키워드를 통해 생성되는 Class나 인스턴스 변수들을 저장하는 공간이다.
해당 영역에서 참조되지 않거나 참조가 해제된 요소들을 대상으로 GC가 발생한다.
> [!INFO]
> JVM Stack에서 저장되는 지역 변수나 데이터 등은 실제로는 Heap 메모리에 대한 참조 주소값만을 지니고 있으며, 해당 데이터를 조회할 때에는 Stack 영역에 명시된 Heap 메모리 주소값을 통해 Heap 저장된 실제 인스턴스를 조회하게 되는 것이다.

**Heap 구조**
Heap 영역은 아래와 같은 구조로 이루어져 있다.

![](images/Pasted%20image%2020231219024938.png)

- Permanent
	생성된 객체들에 대한 주소값이 저장된 공간이며, ClassLoader에 의해 Loading된 Class나 메소드 들에 대한 Meta 데이터가 저장되어 있다.
	- Reflection API에 의해 Class나 메소드 등을 동적으로 loading 할 때 사용된다.
> [!INFO]
> **Reflection API**
> 
> 객체를 통해 Class의 정보를 분석해내는 기법
> 컴파일된 바이트코드를 기반으로 관련된 Class 정보를 식별한다.
> 

- New/Young Generation
	생명주기가 짧은 객체들을 GC 대상으로 하는 영역이다.
	- 여기서 발생하는 GC가 Minor GC 이다.
	- Eden : 객체가 최초로 생성되는 공간
	- Survivor 0, 1 : Eden에서 참조되는 체들의 저장 공간
> [!INFO]
> Eden 영역이 가득 찰 경우 Survivor 영역으로 복사 한 후 나머지 객체들을 제거한다.

- Old
	생명주기가 긴 객체들을 GC 대상으로 하는 영역이다.
	- New/Young 영역의 객체들에 일정 기간 참조되고 있는 객체들의 저장 공간 
	- 여기서 발생하는 GC 가 Major GC이다.
### 2-3-5. Method
JVM이 처음 시작 될 때 생성되는 공간으로 바이트코드를 메모리에 적재할 때 초기화 되는 대상들을 저장하는 공간이다.
저장 후, 프로그램이 종료될 때까지 저장된다.
- Class, Interface에 대한 Runtime Constant Pool, static 변수 등이 저장됨
# Ref
- https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8
- https://catsbi.oopy.io/df0df290-9188-45c1-b056-b8fe032d88ca#9d2fe782-ade7-4c8d-aaf6-20b8ac139a72
- https://doozi0316.tistory.com/entry/1%EC%A3%BC%EC%B0%A8-JVM%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EB%A9%B0-%EC%9E%90%EB%B0%94-%EC%BD%94%EB%93%9C%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94-%EA%B2%83%EC%9D%B8%EA%B0%80
- https://backendcode.tistory.com/161
- https://blog.wanzargen.me/16
