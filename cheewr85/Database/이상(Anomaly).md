## 이상 현상(Anomaly)
- 테이블 내의 데이터들이 불필요하게 중복되어 테이블을 조작할 때 발생되는 불일치 현상임

- 테이블을 잘못 설계하여 삽입, 삭제, 갱신할 때 오류가 발생하게 되는 것임

- 예시 테이블

|Employee_ID|Name|Department|Student_Group|
|------|---|---|---|
|123|J.Longfellow|Accounting|Beta Alpha Psi|
|234|B.Rech|Marketing|Marketing Club|
|234|B.Rech|Marketing|Management Club|
|456|A.Bruchs|CIS|Technology Org|
|456|A.Bruchs|CIS|Beta Alpha Psi|


### 삽입 이상(insertion anomaly)
- 원하지 않는 자료가 삽입된다던지, key가 없어 삽입하지 못하는(불필요한 데이터를 추가해야 삽입할 수 있음) 문제점

- 만약 위의 예시 테이블에서 새로운 부서 Engineering이 신설되었고 아직 근무자가 없다고 할 때 이 부서에 관련한 정보는 불필요한 정보를 함께 입력하지 않는 한 위 테이블에 입력할 수 없음

### 삭제 이상(deletion anomaly)
- 하나의 자료만 삭제하고 싶지만, 그 자료가 포함된 튜플 전체가 삭제됨으로 원하지 않는 정보 손실이 발생하는 문제점

- 위의 테이블에서 Accounting 부서에 속한 사람이 J.Longfellow 단 한 명이라고 할 때 J.Longfellow의 정보를 삭제하면 Accounting 부서에 대한 정보도 사라지게 됨

### 갱신 이상(update anomaly)
- 일부만 변경하여 데이터가 불일치하는 모순, 또는 중복되는 튜플이 존재하게 되는 문제점

- 만약에 A.Bruchs의 부서가 CIS에서 Marketing으로 바뀌었다고 가정하면 테이블의 4,5번째 행의 CIS를 둘 다 바꾸지 않고 하나만 바꾼다면 A.Bruchs의 부서는 어느 부서에 속해있는지 알 수 없게됨
