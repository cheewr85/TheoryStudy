# SQL JOIN
> 두 개 이상의 테이블 또는 데이터베이스를 연결하여 데이터를 검색하는 방법
> 보통 Primary Key 혹은 Foreign Key로 두 테이블 연결
> 테이블을 연결하려면 적어도 하나의 칼럼 공유 

## Inner Join
![process address space](/sejigner/img/db/join/1.png)

```
SELECT
A.NAME, B.AGE
FROM EX_TABLE A
INNER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```


- 교집합을 구하는 것 기준 테이블과 Join한 테이블의 중복된 값을 보여줌
## Left Outer Join
![process address space](/sejigner/img/db/join/2.png)

```
SELECT
A.NAME, B.AGE
FROM EX_TABLE A
LEFT OUTER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```

- 왼쪽 테이블을 기준으로 JOIN
- 기준테이블 값(왼쪽) + 오른쪽 테이블과 기준테이블의 교집합
## Right Outer Join
![process address space](/sejigner/img/db/join/3.png)

```
SELECT
A.NAME, B.AGE
FROM EX_TABLE A
RIGHT OUTER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```

- 오른쪽 테이블을 기준으로 JOIN
- 기준테이블 값(오른쪽) + 테이블과 왼쪽 기준테이블의 교집합
## Full Outer Join
![process address space](/sejigner/img/db/join/3.png)

```
SELECT
A.NAME, B.AGE
FROM EX_TABLE A
FULL OUTER JOIN JOIN_TABLE B ON A.NO_EMP = B.NO_EMP
```

- 모든 테이블의 합집합
- 기준 테이블을 따지는 것이 의미가 없음
## Cross Join
![process address space](/sejigner/img/db/join/4.png)

```
SELECT
A.NAME, B.AGE
FROM EX_TABLE A
CROSS JOIN JOIN_TABLE B
```

- 모든 경우의 수를 표현
- A 테이블에 N개, B 테이블에 M개의 데이터가 있다면 결과값의 개수는 N*M
## Self Join
![process address space](/sejigner/img/db/join/5.png)

```
SELECT
A.NAME, B.AGE
FROM EX_TABLE A, EX_TABLE B
```

- 기준 테이블이 자기 자신과 조인
- 기준 테이블을 다양하게 변형시켜 활용할 때 사용

> Written with [StackEdit](https://stackedit.io/).
