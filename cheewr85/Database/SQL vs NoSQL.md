## 데이터베이스
- SQL, NOSQL을 알아보기 앞서 데이터베이스에 간단히 알아보자면

- 데이터베이스는 데이터의 모음을 말함, 컴퓨터에 저장되는 조직화된 데이터 모음이라고 할 수 있음

- 이렇게 데이터를 조직화하면 데이터에 의미가 생기고 대량의 데이터를 효율적으로 관리할 수 있게됨

- 이런식으로 데이터를 조직화하는 방식에는 여러가지가 있음, 데이터베이스를 만들고 관리하는 방식에 따라 데이터베이스 유형을 구분할 수 있음

### 데이터베이스의 유형의 구분
- 데이터베이스는 주로 아래와 같이 구분됨

   - 관계형 데이터베이스 vs 비관계형 데이터베이스

   - SQL 기반 데이터베이스 vs NoSQL 기반 데이터베이스

   - 관계형 데이터베이스 vs NoSQL

   - SQL vs NoSQL

- 크게 보면 관계형 데이터베이스와 비관계형 데이터베이스로 구분하며 정리함

### 데이터베이스 관리 시스템(DBMS)
- 이런 개념적인 데이터베이스를 실질적으로 구현하기 위해서 일반적으로 데이터베이스 관리 시스템(DBMS)이라는 것을 사용함

- 연결할 애플리케이션, 데이터, DBMS 솔루션을 하나로 묶어 데이터베이스 시스템이라고 하고 단축해서 데이터베이스라고 함

### 정리
- 데이터베이스란 원시적인 의미로는 데이터베이스의 모음을 뜻하지만, 데이터를 조직하는 유형별로, 혹은 DBMS 솔루션까지 묶어서 데이터베이스라고 부름


## 관계형 데이터베이스

![one](/cheewr85/img/Database/two.png)

- 데이터베이스 유형 중 관계형 데이터베이스가 있음

- 관계형 데이터베이스는 고정된 행(row)과 열(column)로 구성된 테이블에 데이터를 저장함

- 각 열은 하나의 속성에 대한 정보를 저장하고, 행에는 각 열의 데이터 형식에 맞는 데이터가 저장됨

- 관계형 데이터베이스에서는 테이블의 구조와 데이터 타입 등을 사전에 정의함, 그릭고 테이블에 정의된 내용에 알맞은 형태의 데이터만 삽입할 수 있음

- 특정한 형식을 지키기 때문에, 데이터를 정확히 입력했다면 데이터를 사용할 때에는 매우 수월함

### 관계형 데이터베이스 관리 시스템
- 관계형 데이터베이스의 대표적인 DBMS는 아래와 같고, 관계형 데이터베이스 관리 시스템을 따로 RDBMS라고 부름

- MySQL

- Oracle

- SQLite

- MariaDB

- PostgresSQL

### 관계형 데이터베이스는 SQL 사용
- 관계형 데이터베이스와 상호작용할 때 SQL을 사용할 수 있음

- 즉, 관계형 데이터베이스에서는 스키마가 뚜렷하게 보인다는 말임, 그래서 관계형 데이터베이스에서는 테이블 간의 관계를 직관적으로 파악할 수 있음

## 정리
- 데이터베이스 유형 중 관계형 데이터베이스는 테이블 기반으로 데이터를 저장함, 그리고 SQL을 사용해서 데이터를 다룰 수 있음

- 테이블의 관계가 구조화된 데이터의 모음이기 때문에 구조화된 쿼리 언어를 사용할 수 있음

![one](/cheewr85/img/Database/three.png)

### 관계형 데이터베이스는 SQL?
- 관계형 데이터베이스는 데이터베이스의 한 유형을 말함, 하나의 데이터 조직화 컨셉임

- RDBMS 솔루션까지 묶어서 관계형 데이터베이스라고도 함(MySQL은 관계형 데이터베이스다라고도 말함)

- 이러한 관계형 데이터베이스에서는 모두 초창기 관계 데이터베이스 시스템을 위해 만들어진 SQL이라는 언어를 사용하므로 관계형 데이터베이스를 SQL이라고 부르는 것

- SQL은 관계형 데이터베이스 전용 프로그래밍 언어임

- RDBMS 프로그램에서 기본적인 SQL을 지원함, 그래서 관계형 데이터베이스를 SQL이라고 부름


## 비관계형 데이터베이스

![one](/cheewr85/img/Database/four.png)

- 비관계형 데이터베이스는 관계형 데이터베이스 유형을 뺀 나머지 유형을 총칭함

- 관계형 데이터베이스가 아닌 모든 곳에서는 NoSQL인 것임

- 위에서 말한것처럼 비관계형 데이터베이스를 칭할 때 NoSQL라고도 함

- NoSQL 데이터베이스란 용어를 보통 비관계형 데이터베이스(관계형 데이터베이스 이외의 형식으로 데이터를 저장하는 데이터베이스)를 지칭할 때 사용함

- NoSQL 데이터베이스 또는 비관계형 데이터베이스로도 관계 데이터를 저장할 수 있긴함

### NoSQL 데이터베이스
- NoSQL 데이터베이스는 표 형식이 아니며, 관계형 테이블과는 다른 방식으로 데이터를 저장함

- 주로 데이터가 고정되어 있지 않은 데이터베이스를 가르킴, 데이터 모델에 따라 유형이 다양함

- 문서, 키 값, 와이드 컬럼, 그래프가 있음

- 그렇다고 NoSQL에 스키마가 반드시 없는 것은 아님

- 유연한 스키마를 제공하며, 대량의 데이터와 높은 사용자 부하에서도 손쉽게 확장이 가능함

- 관계형 데이터베이스는 데이터를 입력할 때 스키마에 맞게 입력해야 하는 반면 NoSQL에서는 데이터를 읽어올 때 스키마에 따라 데이터를 읽어옴(이런 방식을 schema on read라고도 함)

- 읽어올 떄에만 데이터 스키마가 사용된다고 하여, 데이터를 쓸 때 정해진 방식이 없다는 의미는 아님, 데이터를 입력하는 방식에 따라 데이터를 읽어올 때 영향을 미침


## NoSQL 데이터베이스 유형

### Key-Value 타입
- 속성을 key-value의 쌍으로 나타내는 데이터를 배열의 형태로 저장함

- key는 속성 이름을 뜻하고, value는 속성에 연결된 데이터 값을 의미함

- Redis, Dynamo 등이 대표적인 Key-Value 형식의 데이터베이스임

### 문서형(Document) 데이터베이스
- 데이터를 테이블이 아닌 문서처럼 저장하는 데이터베이스를 의미함

- 많은 문서형 데이터베이스에서 JSON과 유사한 형식의 데이터를 문서화하여 저장함

- 각각의 문서는 하나의 속성에 대한 데이터를 가지고 있고, 컬렉션이라고 하는 그룹으로 묶어서 관리함

- 대표적인 문서형 데이터베이스에는 MongoDB가 있음

### Wide-Column Store 데이터베이스
- 데이터베이스의 열에 대한 데이터를 집중적으로 관리하는 데이터베이스임

- 각 열에는 key-value 형식으로 데이터가 저장되고, 컬럼 패밀리라고 하는 열의 집합체 단위로 데이터를 처리할 수 있음

- 하나의 행에 많은 열을 포함할 수 있어서 유연성이 높음

- 데이터 처리에 필요한 열을 유연하게 선택할 수 있다는 점에서 규모가 큰 데이터 분석에 주로 사용되는 데이터베이스 형식임

- 대표적인 wide-column 데이터베이스에는 Cassandra, HBase가 있음

### 그래프 데이터베이스
- 자료구조의 그래프와 비슷한 형식으로 데이터 간의 관계를 구성하는 데이터베이스임

- 노드(nodes)에 속성(entities)별로 데이터를 저장함

- 각 노드간 관계는 선(edge)으로 표현함

- 대표적인 그래프 데이터베이스에는 Neo4J, InfiniteGraph가 있음


## SQL과 NoSQL 데이터베이스 차이점

### 데이터 저장(Storage)
- NoSQL은 key-value, document, wide-column, graph 등의 방식으로 데이터를 저장함

- 관계형 데이터베이스는 SQL을 이용해서 데이터를 테이블에 저장함

- 미리 작성된 스키마를 기반으로 정해진 형식에 맞게 데이터를 저장해야함

### 스키마(Schema)
- SQL을 사용하려면, 고정된 형식의 스키마가 필요함

- 다시 말해, 처리하려는 데이터 속성별로 열(column)에 대한 정보를 미리 정해두어야 함

- 스키마는 나중에 변경할 수 있지만, 이 경우 데이터베이스 전체를 수정하거나 오프라인으로 전환할 필요가 있음

- NoSQL은 관계형 데이터베이스보다 동적으로 스키마의 형태를 관리할 수 있음

- 행을 추가할 때 즉시 새로운 열을 추가할 수 있고, 개별 속성에 대해서 모든 열에 대한 데이터를 반드시 입력하지 않아도 됨

### 쿼리(Querying)
- 쿼리는 데이터베이스에 대해서 정보를 요청하는 질의문임

- 관계형 데이터베이스는 테이블의 형식과 테이블간의 관계에 맞춰 데이터를 요청해야함

- 그래서 정보를 요청할 때, SQL과 같이 구조화된 쿼리 언어를 사용함

- 비관계형 데이터베이스의 쿼리는 데이터 그룹 자체를 조회하는 것에 초점을 두고 있음

- 그래서 구조화 되지 않은 쿼리 언어로도 데이터 요청이 가능함

- UnQL(UnStructured Query Language)이라고 말하기도 함

### 확장성(Scalability)
- 일반적으로 SQL 기반의 관계형 데이터베이스는 수직적으로 확장함

- 높은 메모리, CPU를 사용하는 확장이라고도 함

- 데이터베이스가 구축된 하드웨어의 성능을 많이 이용하기 때문에 비용이 많이 듬

- 여러 서버에 걸쳐서 데이터베이스의 관계를 정의할 수 있지만, 매우 복잡하고 시간이 많이 소모됨

- NoSQL로 구성된 데이터베이스는 수평적으로 확장함

- 보다 값싼 서버 증설 또는 클라우드 서비스를 이용하는 확장이라고도 함

- NoSQL 데이터베이스를 위한 서버를 추가적으로 구축하면, 많은 트래픽을 보다 편리하게 처리할 수 있음

- 그리고 저렴한 범용 하드웨어나 클라우드 기반의 인스턴스에 NoSQL 데이터베이스를 호스팅할 수 있어서, 수직적 확장보다 상대적으로 비용이 저렴함

## SQL vs NoSQL
- 데이터베이스를 구축하는 방법을 선택하는 것에 완벽한 솔루션은 없음

- 그렇기 때문에 많은 개발자들은 유저의 요구를 충족하기 위해 관계형, 비관계형 데이터베이스를 모두 사용하여 서비스에 맞게 설계하고 있음

- NoSQL 기반의 비관계형 데이터베이스가 확장성이나 속도면에서 더 뛰어남

- 그러나 고차원으로 구조화된 SQL 기반의 데이터베이스가 더 좋은 성능을 보여주는 서비스도 있음

- 여러 사용 사례를 살펴보고 적절한 데이터베이스를 선택하는 것이 중요함

## SQL 기반의 관계형 데이터베이스를 사용하는 케이스

### 데이터베이스의 ACID 성질을 준수해야 하는 경우
- ACID는 Atomicity(원자성), Consistency(일관성), Isolation(격리성), Durability(지속성)을 의미함

- 각 단어는 데이터베이스에서 실행되는 하나의 트랜잭션(Transaction)에 의한 상태의 변화를 수행하는 과정에서, 안전성을 보장하기 위해 필요한 성질임

- SQL을 사용하면 데이터베이스와 상호 작용하는 방식을 정확하게 규정할 수 있기 때문에, 데이터베이스에서 데이터를 처리할 때 발생할 수 있는 예외적인 상황을 줄이고, 데이터베이스의 무결성을 보호할 수 있음

- 전자 상거래를 비롯한 모든 금융 서비스를 위한 소프트웨어 개발에서는 반드시 데이터베이스의 ACID 성질을 준수해야함

- 그래서 이런 경우에는 일반적으로 SQL을 이용한 관계형 데이터베이스를 사용함

### 소프트웨어에 사용되는 데이터가 구조적이고 일관적인 경우
- 소프트웨어(프로젝트)의 규모가 많은 서버를 필요로 하지 않고 일관된 데이터를 사용하는 경우, 관계형 데이터베이스를 사용하는 경우가 많음

- 다양한 데이터 유형과 높은 트래픽을 지원하도록 설계된 NoSQL 데이터베이스를 사용해야만 하는 이유가 없기 때문임

## NoSQL 기반의 비관계형 데이터베이스를 사용하는 케이스

### 데이터 구조가 거의 또는 전혀 없는 대용량의 데이터를 저장하는 경우
- 대부분의 NoSQL 데이터베이스는 저장할 수 있는 데이터의 유형에 제한이 없음

- 필요에 따라, 언제든지 데이터의 새 유형을 추가할 수 있음

- 소프트웨어 개발에 정형화 되지 않은 많은 양의 데이터가 필요한 경우, NoSQL을 적용하는 것이 더 효율적일 수 있음

### 클라우드 컴퓨팅 및 저장공간을 최대한 활용하는 경우
- 클라우드 기반으로 데이터베이스 저장소를 구축하면, 저렴한 비용의 솔루션을 제공받을 수 있음

- 소프트웨어에 데이터베이스의 확장성이 중요하다면, 별다른 번거로움 없이 확장할 수 있는 NoSQL 데이터베이스를 사용하는 것이 좋음

### 빠르게 서비스를 구축하는 과정에서 데이터 구조를 자주 업데이트 하는 경우
- NoSQL 데이터베이스의 경우 스키마를 미리 준비할 필요가 없기 때문에 빠르게 개발하는 과정에 매우 유리함

- 시장에 빠르게 프로토타입을 출시해야 하는 경우가 이에 해당함

- 또한 소프트웨어 버전별로 많은 다운타임(데이터베이스 서버를 오프라인으로 전환하여 데이터 처리를 진행하는 작업 시간)없이 데이터 구조를 자주 업데이트 해야하는 경우, 스키마를 매번 수정해야 하는 관계형 데이터베이스보다 NoSQL 기반의 비관계형 데이터베이스를 사용하는 게 더 적합함

 