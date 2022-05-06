## Join
- 둘 이상의 테이블을 연결해서 데이터를 검색하는 방법, 연결하려면 테이블들이 적어도 하나의 컬럼을 공유하고 있어야함

- 이 공유하고 있는 컬럼을 PK 또는 FK값으로 사용 

- 예시 테이블

- A 테이블

|ID|ENAME|
|---|---|
|1|AAA|
|2|BBB|
|3|CCC|

- B 테이블

|ID|KNAME|
|---|---|
|1|가|
|2|나|
|4|라|
|5|마|

### INNER JOIN
- 교집합, 공통적인 부분만 SELECT 됨(내부 조인), 두 테이블 조인할 때, 두 테이블에 모두 지정한 열의 데이터가 있어야함

```SQL
SELECT A.ID, A.ENAME, A.KNAME
FROM A INNER JOIN B
ON A.ID = B.ID;
```

|ID|ENAME|KNAME|
|---|---|---|
|1|AAA|가|
|2|BBB|나|


### LEFT JOIN
- 조인기준 왼쪽에 있는거 다 SELECT 됨(공통적인 부분 + LEFT에 있는거만)

```SQL
SELECT A.ID, A.ENAME, A.KNAME
FROM A LEFT OUTER JOIN B
ON A.ID = B.ID;
```

|ID|ENAME|KNAME|
|---|---|---|
|1|AAA|가|
|2|BBB|나|
|3|CCC|NULL|

- 조인기준 왼쪽에 있는거만 SELECT됨(LEFT에 있는거만 : A-B), LEFT가 가지고 있는 것 중 공통적인 부분을 제외한 값

- 그냥 LEFT JOIN 값 중에서 WHERE 조건으로 NULL인 값을 조회, B가 NULL의 값을 가지고 있는건 A만 가지고 있는 값이니까

```SQL
SELECT A.ID, A.ENAME, A.KNAME
FROM A LEFT OUTER JOIN B
ON A.ID = B.ID;
WHERE B.ID IS NULL
```

|ID|ENAME|KNAME|
|---|---|---|
|3|CCC|NULL|

### RIGHT JOIN
- 조인기준 오른쪽에 있는거 다 SELECT 됨(공통적인 부분 + RIGHT에 있는거만)

```SQL
SELECT A.ID, A.ENAME, A.KNAME
FROM A RIGHT OUTER JOIN B
ON A.ID = B.ID;
```

|ID|ENAME|KNAME|
|---|---|---|
|1|AAA|가|
|2|BBB|나|
|4|NULL|라|
|5|NULL|마|

- 조인기준 오른쪽에 있는거만 SELECT 됨(RIGHT에 있는거만 : B-A), RIGHT가 가지고 있는 것 중 공통적인 부분을 제외한 값

- 그냥 RIGHT JOIN 값 중에서 WHERE 조건으로 NULL인 값을 조회

```SQL
SELECT A.ID, A.ENAME, A.KNAME
FROM A RIGHT OUTER JOIN B
ON A.ID = B.ID
WHERE A.ID IS NULL
```

|ID|ENAME|KNAME|
|---|---|---|
|4|NULL|라|
|5|NULL|마|


### OUTER JOIN
- A테이블이 가지고 있는거 B테이블이 가지고 있느거 둘 다 SELECT, 두 테이블을 조인할 때, 1개의 테이블에만 데이터가 있어도 결과가 나옴

```SQL
SELECT A.ID, A.ENAME, A.KNAME
FROM A FULL OUTER JOIN B
ON A.ID = B.ID
```

|ID|ENAME|KNAME|
|---|---|---|
|1|AAA|가|
|2|BBB|나|
|3|CCC|NULL|
|4|NULL|라|
|5|NULL|마|

- 오른쪽에 있는거만 + 왼쪽에 있는거만 SELECT, FULL OUTER가 가지고 있는 것 중 공통적인 부분을 제외한 값

```SQL
SELECT A.ID, A.ENAME, A.KNAME
FROM A FULL OUTER JOIN B
ON A.ID = B.ID
WHERE A.ID IS NULL OR B.ID IS NULL
```

|ID|ENAME|KNAME|
|---|---|---|
|3|CCC|NULL|
|4|NULL|라|
|5|NULL|마|

### CROSS JOIN
- 한 쪽 테이블의 모든 행과 다른 쪽 테이블의 모든 행을 조인하는 기능

- 양쪽의 데이터를 곱집합을 함

```SQL
SELECT * FROM A CROSS JOIN B
```

|ID|ENAME|ID|KNAME|
|---|---|---|---|
|1|AAA|1|가|
|2|BBB|1|가|
|3|CCC|1|가|
|1|AAA|2|나|
|2|BBB|2|나|
|3|CCC|2|나|
|1|AAA|4|라|
|2|BBB|4|라|
|3|CCC|4|라|
|1|AAA|5|마|
|2|BBB|5|마|
|3|CCC|5|마|

### SELF JOIN
- 자신이 자신과 조인한다는 의미로, 1개의 테이블을 사용함, 자체 조인이 됨

- 특별한 문법이 존재하지 않고 INNER JOIN을 응용해서 사용할 수 있음

- 자신의 테이블을 참조해서 아래와 같이 셀프 조인을 활용, 하나의 테이블로 사원과 직속상관의 관계 정리 가능

- 아래와 같이 id와 superior_id 활용, 셀프 조인으로 묶음

|id|name|superior_id|
|---|---|---|
|1|김사장|NULL|
|2|정과장|1|
|3|최대리|2|
|4|이대리|2|
|5|박고문|NULL|

```SQL
SELECT em1.id, em1.name, em2.name
FROM employee em1
JOIN employee em2
ON em1.superior_id = em2.id;
```

|id|name|name|
|---|---|---|
|2|정과장|김사장|
|3|최대리|정과장|
|4|이대리|정과장|