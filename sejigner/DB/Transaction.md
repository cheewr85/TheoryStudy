# Transaction
트랜잭션
> DB의 상태를 변화시키는 작업의 단위

상태의 변화 : SQL 질의어를 통하여 DB에 접근
- SELECT
- INSERT
- DELETE
- UPDATE

작업 단위 : 여러 개의 SQL 명령문으로 구성
>A가 B에게 만원을 송금한다고 가정하면,  
>A의 계좌에서는 만원을 차감하는 UPDATE문을, B의 계좌에서는 만원을 추가하는 UPDATE문을 실행해야 한다.
>두 가지 쿼리가 모두 실행되면, DB에서 commit을 실행하여 트랜잭션이 성공적으로 DB에 반영되었음을 알림
>트랜잭션을 구성하는 쿼리문 중 하나라도 실패하면 트랜잭션의 모든 쿼리문을 취소하고 초기 상태로 돌려놓는 것을 Rollback이라고 한다.

### 트랜잭션의 특성
- 원자성 : 트랜잭션이 DB에 모두 반영되거나, 아예 반영되지 않아야 함
- 일관성 : 트랜잭션의 결과는 항상 일관성 있어야 함
- 독립성 : 복수의 트랜잭션이 병행 실행되고 있을 때, 트랜잭션끼리 간섭이 일어나서는 안됨
- 지속성 : 완료된 트랜잭션의 결과는 DB에 영구적으로 반영돼야 함

### DBMS Transaction 관리 전략
![dbms](/sejigner/img/db/transaction/1.png)
DBMS 구조
> 크게 Query Processor (질의 처리기)와 Storage System(저장 시스템)으로 구성
> 입출력 단위 : 고정된 길이의 page 단위
> 저장 공간 : 비휘발성 저장 장치인 disk에 저장하고 일부는 Main Memory에 저장

Page Buffer Manager/Buffer Manager
DBMS Storage system에 속하는 모듈 중 하나, main memory 내 페이지를 관리

UNDO
비정상적인 Transaction이 변경한 page를 원상 복구하는 것
- steal : 수정된 페이지를 언제든 디스크에 쓸 수 있도록 하는 정책
- ¬steal : 수정된 페이지들을 EOT(End of Transaction)까지는 버퍼에 유지시키는 정책

REDO
이미 commit한 transaction의 수정을 재반영하는 복구 작업   
Buffer 관리 정책에 의존
- force : 수정했던 모든 페이지를 Transaction commit과 동시에 disk에 반영하는 정책
redo가 필요 없게 됨
- ¬force : 대부분의 DBMS가 채택하고 있는 commit 시점에 반영하지 않는 정책   
redo가 필요함
