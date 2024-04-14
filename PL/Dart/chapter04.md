# Chapter 04. Classes
# 1. Dart의 클래스
Dart는 기본형 타입 (int, double, bool 등) 을 포함한 모든 요소들이 class 라는 단위로 이루어져 있다. 이는 객체지향 언어인 Dart에서 하나의 객체를 뜻하는 가장 작은 단위이며, Java나 c#과 비슷하게 선언하여 사용할 수 있다.

```dart
class Player {
  String name = "guardjo";
  int age = 29;
}

void main() {
  var player = Player();
  print(player.name);
}
```

위와 같이 `class` 키워드를 사용하여 신규 class를 구성할 수 있으며, 구성된 class는 위와 같이 `Player()` 를 통해 해당 class에 대한 신규 인스턴스를 생성할 수 있다.

> [!INFO]
> **클래스 내 property 타입**
> 
> Dart의 경우 var, dynamic 등의 키워드를 통해 변수 타입을 추후에 추론할 수 있게끔 하는 기능을 제공하나, 이는 주로 함수 내에서의 지역 변수에서 사용하는 것을 권고하며, Class 내의 필드 타입에는 명시적으로 타입을 지정해서 선언해주어야 한다.


## 1-1. Class 내에서의 필드 값 조회

```dart
class Player {
  String name = "guardjo";
  int age = 29;

  void printInfo() {
    print("Player {name : $name, age : $age}");
  }
}

  

void main() {
  var player = Player();
  print(player.name);
  player.printInfo();
}
```

위 Class 내에서 특정 메소드가 해당 Class의 필드 값을 필요로 할 경우 위와 같이 바로 사용할 수 있다.
- `this` 키워드를 사용해도 되나, 이는 주로 해당 메소드 내에 필드명과 동일한 이름의 변수가 선언되어 있을 경우에 사용한다.
- ex) `"Player name is ${this.name}"`
# 2. Constructor
Dart에서는 다른 객체지향 언어들과 마찬가지로 Class 에 대한 인스턴스 생성 시 생성 메소드를 별도로 정의할 수 있다.

```dart
class Player {
  late String name;
  late int age;
  
  Player(String name, int age) {
    this.name = name;
    this.age = age;
  }
...
}

void main() {
  var player = Player("Tester", 29);
  print(player.name);
  player.printInfo();
}
```

위 코드를 보면 이전에 만든 Player class 내에 필요한 property 값들을 주입해주는 생성자 메소드이다. Java 등과 비슷하게 Class 명으로 메소드명을 지정하며, 필요한 값들을 argument로 받아온다.
단, 이 때 Dart는 기본적으로 빈 값을 허용하지 않기에 property 구성 부분에 `late` 키워드를 추가하여 추후 생성자에서 값을 주입해주도록 지연시킨다.

또한 Dart에서는 아래와 같이 생성자를 보다 간결하게 구성할 수 있다.

```dart
class Player {
  String name;
  int age;

  Player(this.name, this.age);
  ...
}
```

위와 같이 사용할 경우, 각 인자의 타입과 위치가 일치해야 하며 해당 타입의 해당 property 값을 주입하여 생성하게 된다.
- 또한 dart 내부적으로 해당 property들에 값을 주입하는 것을 알기에, `late` 키워드가 필요 없어진다.
# 3. Constructor Named Parameters
Dart에서는 생성자 또한 일반 함수 메소드와 같이 위치 기반 방식 뿐만 아니라 이름 기반 방식인 `Named Parameters`를 제공한다.

```dart
class Player {
  String name;
  int age;

  Player({required this.name, required this.age});

  void printInfo() {
    print("Player {name : $name, age : $age}");
  }
}

void main() {
  var player = Player(name: "Tester", age: 20);
  print(player.name);
  player.printInfo();
}
```

일반 함수에서 `Named Parameters`를 지정하는 것과 동일하게 생성자 내 인자 항목에 `{}` 를 통해 `Named Parameters`를 지정할 수 있다.

# 4. Named Constructor
Dart에서는 일반적인 생성자 뿐만 아니라 사용자 지정 이름의 생성자를 구성할 수 있다.

```dart
class Player {
  String name;
  int age;

  Player({required this.name, required this.age});
  
  Player.TestPlayer()
      : this.name = "Tester",
        this.age = 100;

  void printInfo() {
    print("Player {name : $name, age : $age}");
  }
}

void main() {
  var player = Player(name: "Tester", age: 20);
  print(player.name);
  player.printInfo();

  var tester = Player.TestPlayer();
  tester.printInfo();
}
```

위와 같이 `Class명.생성자명` 을 통해 사용자가 직접 이름을 지정하여 생성자를 구성할 수 있으며, `:` 기호를 사용하여 해당 class의 property들을 주입해줄 수 있다.

# 5. Cascade Operator
앞서 구성한 Player 객체 내 property들을 수정하려먼 일반적으로는 아래와 같이 각 property 값을 수정한다.

```dart
void main() {
  var updatePlayer = Player(name: "old", age: 13);
  updatePlayer.name = "new";
  updatePlayer.age = 14;

  updatePlayer.printInfo();
}
```

위의 경우 선언된 인스턴스명을 지속적으로 사용하여 해당 인스턴스의 필드 값을 바꾸고 있는데, Dart 에서는 이러한 작업들을 도와줄 수 있는 문법을 제공한다.

```dart
void main() {
  var updatePlayer = Player(name: "old", age: 13)
    ..name = "new"
    ..age = 14;

  updatePlayer.printInfo();
}
```

위와 같이 `..` 이후 해당 인스턴스의 필드나 함수를 요청 할 수 있다.
의미적으로는 첫번째 `.`은 해당 인스턴스를 나타내며 풀이하면` (해당 인스턴스).(필드 or 메소드)` 로 볼 수 있다.
# 6. Enum
여러 필드들을 사용하다보면 (특히 문자열), 개발자의 실수, 혹은 오타 등으로 일부 데이터가 올바르게 구성되지 않는 경우가 발생할 수 있다. 이러한 떄에는 다른 여러 언어들에서도 제공하는 `enum` d을 사용하여, 그러한 실수를 막을 수 있다.

```dart
enum PlayerType { basic, pro }

class Player {
  String name;
  int age;
  PlayerType type = PlayerType.basic;
}
```

Dart에서는 위와 같이 enum을 구성할 수 있다.
enum의 경우 개발자의 실수를 막아줄 뿐만 아니라, IDE에서 자동 완성 등의 지원을 받을 수 있게 되며,  관련 데이터 수정 시에도 일괄적으로 수정할 수 있다는 장점이 존재한다.

# 7. Abstract Class
Dart에서도 다른 객체 지향 언어들과 마찬가지로 추상 클래스를 제공한다.

```dart
abstract class Fruits {
  void printInfo();
}

class Apple extends Fruits {
  @override
  void printInfo() {
    // TODO: implement printInfo
    print("This is Apple");
  }
}
```

위와 같이 `abstract` 키워드를 통해 추상 클래스를 구성할 수 있으며, 그러한 추상 클래스는 `extends` 키워드를 통해 구현체로 구현할 수 있다.
# 7. Inheritance
다른 객체 지향 언어들과 마찬가지로 Dart에서도 상속을 지원한다.

```dart
class Phone {
  final String phoneNumber;

  Phone(this.phoneNumber);

  void printNumber() {
    print("My Phone Number is $phoneNumber");
  }
}

class Iphone extends Phone {
  final String appleId;

  Iphone({required this.appleId, required String phoneNumber})
      : super(phoneNumber);

  @override
  void printNumber() {
    super.printNumber();
    print("My appleId is $appleId");
  }
}
```

위와 같이 `extends` 키워드를 통해 상위 class를 상속 받을 수 있다.
이 때 하위 class는 상위 class의 생성자 메소드(`super`)를 호출하여 상위 요소들을 초기화하여야 한다.
# 8. Mixin
Dart에서는 상속 외에도 Mixin이라는 키워드를 제공한다. Mixin은 기존 상속과는 다르게 부모, 자식 관계 구성이 아닌 그저 해당하는 Class의 property와 method 등을 사용할 수 있도록만 하는 것이다.

```dart
mixin Programmer {
  final String programLanguage = "dart";

  void printProgrammerInfo() {
    print("I'm Programmer, Studying $programLanguage");
  }
}

mixin Student {
  final String schoolName = "Dart Highschool";

  void printStudent() {
    print("I'm Student, in $schoolName");
  }
}

class Human with Programmer, Student {
  String name;

  Human(this.name);
}
```

위와 같이 mixin으로 사용할 class는 `mixin` 키워드를 사용하여 구성해주고 `with` 키워드를 통해 사용할 수 있으며, `with` 이후에 오는 mixin class들의 property와 method 등을 사용할 수 있게 된다.

> [!INFO]
> mixin은 상속 관계가 아니며, 그저 해당 class들의 정보들을 중복 코드 없이 사용할 수 있도록 제공해주며, 기본적으로 생성자가 없는 class만 mixin을 할 수 있다.

