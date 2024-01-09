> Effective Java 관련 학습 : 모든 객체의 공통 메소드

# 1. equals는 일반 규약을 지켜 재정의하라
두 객체 인스턴스가 동일한지 여부를 판단하기 위해 사용되는 equals() 메소드는 Java 최상단 객체인 Object에 정의된 메소드이다.

final이 아닌 메소드이기에 누구나 Override할 수 있으며, 이 때 `equals()` 메소드의 기본 규약을 지켜 Overriding 해야 한다.
- 이는 Object의 `equals()`가 규약대로 구현되어 있다는 가정하에 Collection이나 일부 다른 객체들이 구현되어 있기 때문이다.

## 1-1. equals 기본 규약
`equals()`는 동치 관계 (Equivalence relation)을 구현하며, 이를 위해 아래와 같은 사항을 만족해야 한다.
1. **Reflexivity (반사성)**
	null이 아닌 모든 참조 값 x 에 대해 x.equals(x) 가 true여야 함
2. **Symmetry (대칭성)**
	null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true이면 y.equals(x) 도 true여야 함
3. **Transitivity (추이성)**
	null 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true, y.equals(z)도 true이면 x.equals(z)도 true여야 함
4. **Consistency (일관성)**
	null 아닌 모든 참조 값 x, y에 대해 x.equals(y)를 반복해서 호출해도 항상 True이거나 False 여야 함
5. **Not-null (null 아님)**
	null 아닌 모든 참조 값 x에 대해 x.equals(null)은 false 여야 함

> [!NOTE]
> **equals() 재정의**
> 
> 일반적으로 대부분의 객체는 `equals()`를 재정의할 필요가 없다. 이미 대부분의 객체가 기존 요소로 충분하기 때문이다. 그러나, 일부 값 객체 (DTO 등)에서는 논리적인 값 동치 작업이 필요하기에 그런 시점에선 `equals()`를 재정의하여 사용한다.