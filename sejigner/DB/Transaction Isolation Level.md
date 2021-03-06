# Transaction Isolation Level
격리 수준이란?
> 트랜잭션에서 일관성 없는 데이터를 허용하는 수준

격리 수준의 필요성
---
각 트랜잭션은 독립적으로 동작해야하므로, 한 트랜잭션이 DB를 다룰 때 Locking을 통해 다른 트랜잭션이 관여하지 않도록 해야한다.

그러나 트랜잭션이 동시다발적으로 수행되는 동안 무조건적인 Locking은 DB 전체의 성능을 떨어뜨리게 된다.

성능과 DB의 일관성 사이에서 적절한 Locking 범위를 설정하는 것이 필요하다.

격리 수준
---
1. Read Uncommitted (레벨 0)

- 특정 데이터에 SELECT이 수행되는 동안 그 데이터에는 Shared Lock이 걸리지 않는 수준
 - 트랜잭션이 처리하고 있고, 아직 Commit이 되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용
 - DB의 일관성 유지가 불가능
 
 2. Read Committed (레벨 1)
 
 - 특정 데이터에 SELECT이 수행되는 동안 그 데이터에 Shared Lock이 걸리는 수준
 - 트랜잭션이 한 데이터를 다루고 있으면 다른 트랜잭션이 접근할 수 없어서 commit까지 대기
 - SQL 서버의 Default 격리 수준
 
 3. Repeatable Read (레벨 2)
 
 - 트랜잭션 내부에서 SELECT되는 모든 데이터에 Shared Lock이 걸리는 수준
 - 완벽한 DB 읽기 일관성 제공
 - 다른 사용자 또는 트랜잭션이 먼저 동작 중인 트랜잭션 영역에서 SELECT되는 데이터 접근 불가능

격리 수준 선택 시 고려 사항
---
> 격리 수준 -> 동시성, 데이터 무결성
- 격리 수준을 낮추면 동시성이 높아지는 반면, 데이터 무결성은 떨어짐
- 격리 수준을 높이면 동시성이 떨어지는 반면, 데이터 무결성은 높아짐
- 격리 수준이 높아 동시성이 떨어지면, 결국 트랜잭션 수행 비용이 커짐
- 낮은 격리 수준으로 데이터 무결성이 떨어질 때는 다음과 같은 현상 발생
	- Dirty Read
	> 수정되었으나 commit 처리되지 않은 데이터를 다른 트랜잭션이 읽을 경우 
	
	- Non-Repeatable Read
	> 한  트랜잭션이 쿼리를 두 번 연속 수행할 때, 두 쿼리 실행 사이에 다른 트랜잭션에 의해 데이터가 수정되어, 두 쿼리의 결과가 다를 경우
	> 데이터의 일관성이 훼손됨

	- Phantom Read
	> 한 트랜잭션이 특정 범위의 레코드를 여러번 읽었을 때, 앞쪽 쿼리에서는 잡히지 않던 데이터가 뒷쪽 쿼리에서는 잡히는 현상
	> 트랜잭션 도중 다른 트랜잭션에 의해 새로운 레코드가 삽입되어 없던 레코드가 나타나게 됨
