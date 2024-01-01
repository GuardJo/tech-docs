> Partitioning 과 Sharding 내용 정리
# 1. Partitioning
대용량 데이터를 괸리하는 DB 구조에서 서버의 자원을 분산하여 관리하는 기법 중 하나로, 하나의 테이블을 여러 테이블로 나누어서 관리하는 기법이다.

이러한 기법은 DML (데이터 조작어) 의 성능이 개선되는 장점이 있으나, 분할된 테이블들을 조회하기 위한 JOIN 등의 복잡도의 증가, 분할된 데이터 간의 정합성 등의 문제가 발생할 수 있다.

나누는 요소에 따라 크게 수직 파티셔닝과 수평 파티셔닝으로 구분된다.
![](images/Pasted%20image%2020231218023506.png)
## 1-1. Vertical Partitioning
수직 파티셔닝이라고 하며, 테이블의 Column을 기준으로 테이블을 분할하여 관리한다.
주로 성능 개선 뿐만 아니라, 민감한 정보를 제외하거나, 방지하는 등의 작업을 위해서도 쓰인다.
## 1-2. Horizontal Partitioning
수평 파티셔닝이라고 하며, 수직 파티셔닝과는 반대로 테이블의 Row를 기준으로 테이블을 분할하여 관리한다.
주로 DB의 용량이 증가하여, 성능 저하를 막기 위해 쓰인다.
## 1-3. Partitioning 범위
특정 기준으로 파티셔닝을 진행할 때는 주로 아래의 범위들을 기주으로 파티셔닝을 진행한다.
**Range Partitioning**
- 연속적인 값을 기준으로 나눔
- 날짜 및 분기 등

**List Partitioning**
- 특정 목록을 기준으로 나눔
- 나라, 지역 등의 목록

**Hash Partitioning**
- 특정 Column 값을 해싱하여 나눔
- 균일한 분할 가능, 별도의 순서나 범위가 없는 요소에 적합

**Composite Partitioning**
- 위 두 가지 이상의 파티셔닝 범위를 사용
# 2. Sharding
Partitioning과 마찬가지로 대용량의 DB 테이블을 분할하여 관리하는 기법 중 하나로 Partitioning과 달리 기존 테이블과 동일한 형태의 스키마를 지닌 다수의 DB 서버를 구성하는 기법이다.

얼핏 Horizontal Partitioning과 비슷해보이나, Horizotal Prtitioning의 경우 하나의 DB 서버에서 동일한 형태의 테이블을 구성하는 반면, Sharding의 경우 동일한 스키마를 지닌 별도의 DB 서버를 구성하는 것이 차이점이다.

그렇기에 DB 서버에 자체적인 Scale-out 기법이며, 이에 따라 결과적으로 DB 서버 자체의 부하를 분산할 수 있게 된다. 단, 이에 따라 DB 서버별 연결, JOIN 등이 Partitioning에 비해 더욱 복잡하다.

## 2-1. Sharding 종류
물리적으로 분할되어 있는 DB 서버에 접근 할 수 있도록 아래와 같은 종류가 존재한다.
### 2-1-1. Hash Sharding
![](images/Pasted%20image%2020231218023449.png)
Hash Sharding 중 Modulo 연산을 통한 Sharding 방식이 대표적이며, 식별키 값에 대해 modulo 연산을 통해 나온 결과 값을 통해 어느 Shard 서버에 요청할 것인지를 결정한다.
- Sharding된 DB 서버의 개수에 따라 연산이 달라지기에 총 DB 서버 수가 정해져 있을 떄 용이함
### 2-1-2. Range Sharding
![](images/Pasted%20image%2020231218023738.png)
Range Sharding의 경우 식별키들을 특정 범위 별로 묶어 요청한 식별키에 대한 범위 값에 해당하는 Shard 서버를 결정한다.
- 단, 주로 몰리는 식별키가 존재할 경우 특정 DB 서버에만 부하가 몰릴 수 있음
# Ref
- https://hudi.blog/db-partitioning-and-sharding/
- https://aiday.tistory.com/123