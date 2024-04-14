# Chapter02. DataTypes

# 1. 기본타입
Dart는 기본적으로 다른 언어들에서 지원하는 Integer, Double, Boolean, String 등, 대부분의 타입을 지원한다.
```dart
void main() {

  String str = "문자열";

  bool boolean = false;

  int integer = 1;

  double d = 1.1;

  num number = 1;

  number = 1.1;
  number.abs();
}
```
단, 다른 언어들과 다르게 Dart는 기본 타입 또한 하나의 객체로 취급된다. 즉, int, bool, double 등도 실제로 Object이다.
> [!NOTE]
> String 타입은 ''이든 ""이든 여부에 관계 없이 작용된다


그로 인해 별도의 import 없이 선언된 타입변수 자체로 타입 내 함수들을 사용할 수 있다.
- Ex) : `number.abs();`

> [!NOTE]
> **num 타입**
> 
> num 타입의 경우 실제로 int, double 등의 공통 타입 객체이다. 여러 수 관련 함수들을 지니고 있으며 자식 타입으로 int, double 등이 존재한다.
> 
> ![](images/Pasted%20image%2020231221234245.png)
> ![](images/Pasted%20image%2020231221234316.png)

# 2. List 타입
다른 언어들과 마찬가지로 Dart 또한 Collection 관련 타입들을 지원한다.

```dart
void main() {
  var lists = [1, 2, 3, 4];
  // List<int> lists = [1, 2, 3, 4];
}
```

var 키워드 혹은 List 객체 타입 명시를 통해 List 타입의 객체를 초기화 할 수 있다.
또한 다른 언어들과의 차이점으로 Collection If와 Collection For 문을 지원한다.

## Collection If
List 내 데이터 주입 시 if문을 사용하여 특정 조건 하에서만 해당하는 데이터를 주입하도록 한다.
```dart
void main() {
  var giveMeFive = true;
  var lists2 = [1, 2, 3, 4, if (giveMeFive) 5];

  print(lists2);
}
```
위의 경우 giveMeFive 값이 true인 경우, 5를 List에 주입한다.
- 실제 결과
	![](images/Pasted%20image%2020231221235324.png)

## Collection For
List 내에 데이터 주입 시 for문을 사용하여 특정 Collection 내 값들을 주입할 수 있다.
```dart
void main() {
  var oldData = ["file1", "file2"];
  var newData = [
    "file3",
    "file4",
    for (var fileName in oldData) "old_${fileName}"
  ];
  
  print(newData);
}
```
위의 경우 newData List 를 구성 할 때 주입 데이터 내 for 문을 통해 oldData의 값을 추가로 주입한다. 
- 이 때 String Interpolation으로 순회중인 데이터의 값을 주입한다.
> [!NOTE]
> **String Interpolation**
> 
> String 타입의 경우 $ 연산자를 넣어 변수 값을 문자열 내부에 주입할 수 있다. 또한 ${ 수식 } 의 형태로 주입하는 변수에 대한 연산 작업도 수행할 수 있다.
> 
> ```dart
> void main() {
> var name = "Tester";
> var age = 28;
> var profile = "Name : $name, Age : $age, KoreanAge : ${age + 1}";
> print(profile);
> }
>```



# 3. Map 타입
Java의 Map 과 같은 자료구조를 뜻하나 선언 방식은 Javascript나 Typescript의 Object와 흡사하다.
```dart
void main() {
  var map1 = {"name": "tester", "age": 28};

  Map<String, String> map2 = {"key1": "value1", "key2": "Value2"};

  Map<List<int>, String> map3 = {

    [1, 2, 3]: "One Twon Three",

    [4, 5]: "Four, Five"

  };

  print(map2["key1"]);

}
```

위와 같이 다양한 Map 타입을 구성할 수 있으며, var 키워드를 통해 구성되었을 경우에는 내부 데이터 값을 기반으로 추론한다.
- map1의 경우 컴파일러가 Map<String, Object>로 인식한다.
	![](images/Pasted%20image%2020231222002326.png)
	- 이는 String과 int 가 섞여 있어, 이들의 최상위 객체인 Object로 구성한 것임
# 4. Set 타입
Java나 다른 언어들의 Set 타입과 동일하며 List와 다르게 {} 로 구성한다.
```dart
void main() {
  // Set 타입
  var set1 = {1, 2, 3, 4};
  // Set<int> set1 = {1, 2, 3, 4};
  print(set1);
}
```

위와 같이 선언할 수 있으며 List 와의 차이점은 Set의 경우 모든 요소들이 유일한 값이기에 아래와 같이 추가 주입하더라도 이미 값이 존재하기에 추가되지 않는다.
```dart
void main() {
  // Set 타입
  var set1 = {1, 2, 3, 4};
  // Set<int> set1 = {1, 2, 3, 4};
  set1.add(1); // 추가 안됨
  print(set1);
}
```
