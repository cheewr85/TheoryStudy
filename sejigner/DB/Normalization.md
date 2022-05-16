# Normalization
## 정규화(Normalization)
테이블 간 데이터 중복이 나타나지 않도록 데이터를 구조화하는 프로세스를 의미한다.

### 목적
- 중복을 없애 불필요한 데이터를 줄임
- 무결성 유지, 이상 현상 방지
- 논리적이고 직관적인 테이블 구성
- 높은 데이터베이스 구조 확장성

일반적으로 1~3단계의 정규화 과정이 있다.

### 제 1정규화(1NF)
테이블 컬럼이 원자값(하나의 값)을 갖도록 테이블을 분리한다.
제 1정규화에서는 다음 조건을 만족시킨다.

- 릴레이션에 포함된 모든 도메인은 원자값만으로 되어 있어야 함
- 그룹이 모든 속성에 반복 X
- 기본키를 통해 각 집합을 고유하게 식별하도록 함

![1nf-1](sejigner/img/db/normalization/1.png)

1NF의 조건(하나의 원자값)을 만족하기 위해서는 아래와 같이 변경되어야 함

![1nf-2](sejigner/img/db/normalization/2.png)

Customer ID가 고유값으로서 각 집합의 식별을 담당

### 제 2정규화(2NF)
테이블의 모든 컬럼이 완전 함수적 종속을 만족한다.

즉, 테이블의 기본키가 복합키(키1, 키2)로 묶여있을 때, 두 키 중 하나의 키만으로 다른 컬럼을 결정지을 수 있어서는 안된다. 

![2nf-1](sejigner/img/db/normalization/3.png)

Manufacturer와 Model이 키가 되고, 이를 통해 Model Full Name을 알 수 있다.

Manufacturer Country는 Manufacturer에 종속적이지만(부분 함수 종속), Model과는 연관관계가 없으므로, 제 2정규화 조건을 만족시키지 못 한다.

따라서, 다음과 같이 테이블을 분리해야한다.

![2nf-2](sejigner/img/db/normalization/4.png)

이제 각 테이블이 완전 함수적 종속 상태가 되었다.

### 제 3정규화(3NF)
2NF가 끝난 테이블에 이행적 종속이 없도록 한다.
> 이행적 종속 : A -> B, B -> C면 A -> C

다음 두 가지 조건을 만족시켜 제3정규화를 달성한다.

- 릴레이션이 2NF 만족
- 기본키가 아닌 속성은 기본키에 의존해야 함

![3nf-1](sejigner/img/db/normalization/5.png)

위 테이블의 기본키는 Tournament와 Year

Winner는 두 기본키로 구성된 복합키에 종속된다.

문제는, Winner Date of Birth 속성이 기본키가 아닌 Winner에 종속된 것이다.

이는 3NF를 위반하므로, 다음과 같이 분리해야 한다.

![3nf-2](sejigner/img/db/normalization/6.png)

Winner Dates of Birth 라는 독립된 테이블을 두어, Winner가 기본키가 되고, Date of Birth가 기본키에 의존하도록 하여 3NF 조건을 만족한다.
