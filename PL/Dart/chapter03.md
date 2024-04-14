# Chapter03. Functions
# 1. 기본 함수
Dart는 객체 지향 언어에서 제공하는 대부분의 함수 문법과 비슷한 요소로 구성되어 있다.
```dart
String func1(String input) {
  return "Hello $input Function";
}

void testPrint() {

  print("Test Print");

}

void main() {
  var dart = "Dart";
  testPrint();
  print(func1(dart));
}
```

함수 선언 시에는 위와 같이 반환할 타입을 지정해야하며, 입력받아야할 인자가 있을 경우 해당 인자 타입과 이름을 지정해야 한다.
- 반환 타입을 지정하지 않는 경우 void로 명시하며, return 키워드를 사용하지 않는 함수를 작성한다.

## 1-1. 화살표 함수
```dart
String func1(String input) {
  return "Hello $input Function";
}
```

임의의 함수가 위와 같이, 별도의 내부 로직이 존재하지 않고 곧바로 반환하는 함수인 경우 아래와 같이 `=>` 기호를 사용하여 단축해서 정의할 수 있다.

```dart
String func1(String input) => "Hello $input Function";
```

# 2. Named Parameters
Dart에서는 함수 호출 간 입력되는 인자에 대해서 기존 순서 기반 입력 방식 뿐만 아니라, 이름을 통한 입력 방식 제공을 통해 보다 가독성 있는 코드 작성에 도움을 줄 수 있는 기능을 제공한다.

```dart
void printInputs(String input1, String input2, String input3) {
  print("input1 = $input1, input2 = $input2, input3 = $input3");
}

void main() {
  printInputs("test", "dd", "yyy"); // 각 인자값에 대한 명확한 인식이 어려
}
```

기존 순서 기반 입력 방식의 경우 위와 같은 인자들을 지닌 함수를 호출 할 때 순서에 맞는 데이터를 주입해주지 않으면 문제가 발생할 수 있다. 또한 이러한 방식은 현재 입력하는 인자가 어떤 인자인지 명확하게 알기 어려운 문제가 존재한다.

이러한 지점에서 `Named Parameters`, 이름 기반 입력 방식을 사용할 경우 아래와 같이 어떠한 데이터가 어떠한 인자로 주입되는지 명확하게 알 수 있다.

```dart
void printInputs({String input1, String input2, String input3}) {
  print("input1 = $input1, input2 = $input2, input3 = $input3");
}

void main() {
  printInputs(input1: "test", input2: "ddd", input3: "yyyyy");
}
```

이러한 Named Parameter 방식은 위와 같이 함수 선언 부의 인자 부분에 Named Paramters 가 필요한 요소들을 `{}` 로 묶어서 지정할 수 있다.

## 2-1. Named Paramters의 Null처리 방식
`Named Paramters` 방식의 인자 값 주입의 경우, 사용자에게 인자 입력에 대한 권한을 주고 있기에 사용자가 아래와 같이 일부 인자를 입력하지 않을 경우 Null이 존재하게 되기에 추가적인 Null 처리 방식이 필요하다.

```dart
void printInputs({String input1, String input2, String input3}) {
  print("input1 = $input1, input2 = $input2, input3 = $input3");
}

void main() {
  printInputs(input1: "test",); // 일부 인자가 주입되지 않아 null이 주입될 수 있음
}
```

Dart에서는 이러한 상황에서 아래 두 가지 경우로 Null 인자에 대한 처리를 지원한다.

### 2-1-1. Default Value 지정
```dart
void printInputs(
    {String input1 = "input1",
    String input2 = "input2",
    String input3 = "input3"}) {
  print("input1 = $input1, input2 = $input2, input3 = $input3");
}
```

위와 같이 `Named Parameters`로 지정한 함수 인자에 기본값을 지정하여, 해당 인자에 값이 주입되지 않았을 경우에는 지정된 기본값이 주입되도록 한다.

### 2-1-2. required 키워드
Default Value 지정의 경우, 사용자가 특정 인자 값을 주입하지 않아도 괜찮다는 전제하에 구성한 작업이지만, 필수적으로 사용자로부터 인자 값을 주입받아야 할 경우 `required` 키워드를 사용하여 해당 함수를 사용할 때에 필수적으로 값을 주입하도록 구성할 수 있다.

```dart
void printInputRequired(
    {required String input1, required String input2, required String input3}) {
  print("input1 = $input1, input2 = $input2, input3 = $input3");

}
```

위와 같이 인자 타입 앞에 `required` 키워드를 추가함으로써 해당 인자는 반드시 해당 함수 호출자로부터 값을 입력받도록 지정할 수 있다.
- requried 한 인자에 값을 주입하지 않을 경우 아래와 같은 경고를 준다.
	![](images/Pasted%20image%2020231227172821.png)

# 3. Optional Positional Parameters
`Named Parameters`이 아닌 위치 기반 구성에서는 모든 인자가 기본적으로 `required`한 인자로 취급된다. Dart에서는 이러한 위치 기반 구성에서도 일부 인자를 `optional` 하게 구성할 수 있다.

```dart
String printInputOptional(String name, [String? job = "Baeksu"]) =>
    "Hello My name is $name, My job is $job";

void main() {
  var outputStr = printInputOptional("kyeongho");
  print(outputStr);
}
```

위와 같이 `optional` 하게 지정할 인자를 `[]`로 묶어주며, 해당 인자의 데이터타입을 Nullable 하게 지정한 후, default Value를 지정해주는 것으로써 위치 기반 구성에서도 optinal한 인자를 사용할 수 있다.

# 4. QQ Operator
Dart 에서는 기본적으로 모든 데이터 타입이 Null을 허용하지 않는 구조이다.
이러한 구조에서 아래와 같이 Nullable한 데이터 타입을 구성한 후 관련 타입 함수를 사용할 경우, Null 에 대한 처리 작업을 수행해주어야 한다.

```dart
String getUppercaseName(String? name) {
  if (name == null) {
    return "NULL";
  }
  return name.toUpperCase();
}

void main() {
  var name;
  var upperName = getUppercaseName(name);
  print(upperName);
}
```

위 코드의 경우 Null이 들어올 경우 별도 처리를 위한 if문 분기 및 별도 return 을 구성하고 있다.
Dart에서는 이러한 경우 보다 쉽게 처리할 수 있게끔 `??` 연산자를 제공한다

```text
QQ Operator 문법
left ?? right
```

위와 같은 문법으로 구성되며, `left` 가 null일 경우 `right`를 반환하도록 한다.
이를 기반으로 위의 null 처리를 아래처럼 간단하게 개선할 수 있다.

```dart
String getUppercaseName(String? name) {
  return name?.toUpperCase() ?? "NULL";
}
```
## 4-1. QQ equals
Dart 에서는 QQ Operator 뿐만 아니라 QQ equals 라는 연산자도 제공한다.
```text
QQ equals 문법

left ??= right
```

위와 같이 left의 값이 null일 경우 right의 값을 주입해준다.
실제로는 아래와 같이 사용할 수 있다.

```dart
void main() {
  var name;
  name ??= "Null Name";
}
```

위의 경우, name의 값이 할당되지 않은 null 이기에 "Null Name" 이 주입된다.
# 5. Typedef
Dart에서는 모든 데이터 타입에 대해서 사용자가 alias 를 지정할 수 있다.

```dart
typedef ListOfInt = List<int>;

ListOfInt reverseList(ListOfInt list) {
  return list.reversed.toList();
}

void main() {
  print(reverseList([1, 2, 3]));
}
```

위와 같이 `List<int>`로 구성된 데이터타입을 `typedef` 키워드를 사용해 특정한 이름으로 지정할 수 있으며, Dart는 지정된 이름을 알아서 `List<int>` 로 해석하게 된다.