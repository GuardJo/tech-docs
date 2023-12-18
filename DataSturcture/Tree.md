> Tree 자료구조 내용 정리
# 1. Tree란?
계층적인 데이터를 표현하는데 주로 이용되는 자료구조다.

![](images/Pasted%20image%2020231218193252.png)

위와 같은 형태를 취하고 있으며 각각의 데이터를 Node라는 단위로 표현한다.
**Node 종류**
- Root Node
	부모가 없는 최상위 Node
- Leaf Node
	자식이 없는 최하위 Node

**Tree 용어**
- Size : Tree에 포함된 모든 Node의 개수
- Depth : Root Node로부터의 거리
- Height : Depth 중 최대값
- Degree : 각 Node의 간선 개수
## 2. Tree의 특징
Tree는 Node와 Edge로 이루어져 있으며 아래와 같은 특징들이 존재한다.
1. Tree는 하나의 Root Node를 지닌다.
2. Root Node는 0개 이상의 Child Node를 갖는다.
3. child Node 또한 0개 이상의 Child Node를 갖는다.
4. N개의 Node를 지닌 Tree의 N - 1개의 Edge를 갖는다.
5. 모든 Child Node는 1개의 Parent Node만을 갖는다.
6. 모든 Node는 연결되어 있다.
7. 임의의 Node에서 다른 Node로 가는 경로는 1개만 존재한다.
>[!INFO]
>Tree 는 순회가 존재할 수 없기에, 순회가 없는 연결 그래프로 볼 수 있다.
> - *Tree 자체가 결국 최소신장그래프임*
# 3. Tree의 순회
Tree 구조 내의 모든 Node를 1회씩 방문하는 방법으로 아래와 같은 3가지 방법이 존재한다.
## 3-1. Pre-Order Traversal
전위 순회라고 하며, Node -> left -> right 순으로 방문하는 기법이다.

![](images/Pasted%20image%2020231218194827.png)

위의 경우 중앙 Node인 A를 시작으로 왼쪽의 B, C를 거쳐 오른쪽의 E로 넘어가며 Node인 E를 기준으로 좌측인 F와 우측의 G를 순회한다.
## 3-2. In-Order Traversal
중위 순회라고 하며, Left -> Node -> Right 순으로 방문하는 기법이다.

![](images/Pasted%20image%2020231218195548.png)

위의 경우 좌측인 C부터 순회하며 이후 Node인 B를 거쳐 우측의 D를 방문하며, 이후 다시 해당 Node들을 기준으로 Node인 A를 순회후 우측 Sub Tree로 넘어가 다시 좌측부터 F, E, G 순으로 순회한다.
## 3-3. Post-Order Traversal
후위 순회라고 하며, Left -> Right -> Node 순으로 방문하는 기법이다.

![](images/Pasted%20image%2020231218195157.png)

위의 경우 좌측 끝의 C부터 시작하여 우측의 D를 거쳐 Node인 B로 넘어가며, 이후 다시 다음 Sub Tree의 F부터 시작하여, 오른쪽인 G를 타고 Node 인 E 를 거친 후 마지막 Root Node를 방문한다.