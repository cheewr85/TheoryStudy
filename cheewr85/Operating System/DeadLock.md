## 데드락(DeadLock)
- 시스템 자원에 대한 요구가 뒤엉킨 상태

- 둘 이상의 프로세스가 다른 프로세스가 점유하고 있는 자원을 서로 기다릴 때 무한 대기에 빠지는 상황

- 즉, 어떤 프로세스가 자원을 요청 했을 때 그 시각에 그 자원을 사용할 수 없는 상황이 발생할 수 있고 그 때는 프로세스가 대기 상태로 들어가는데 그 대기 상태로 들어간 프로세스들이 실행 상태로 변경 될 수 없을 때 데드락 상태가 생김(멀티 프로그래밍 환경에서 한정된 자원을 얻기 위해 서로 경쟁하는 상황 발생할 경우에도 생김)

-----------

### 데드락(DeadLock)의 발생 조건
- 한 시스템 내에서 아래의 4가지 조건이 동시에 성립할 때 발생함

- 4가지 조건 중 하나라도 성립하지 않도록 만든다면 교착 상태를 해결할 수 있음

#### 상호 배제(Mutual exclusion)
- 자원은 한 번에 한 프로세스만이 사용할 수 있어야함(사용 중인 자원을 다른 프로세스가 사용하려면 요청한 자원이 해제될 때까지 기다려야함)

#### 점유 대기(Hold and wait)
- 최소한 하나의 자원을 점유하고 있으면서 다른 프로세스에 할당되어 사용하고 있는 자원을 추가로 점유하기 위해 대기하는 프로세스가 있어야 함

#### 비선점(No preemption)
- 다른 프로세스에 할당된 자원은 사용이 끝날 때까지 강제로 빼앗을 수 없어야함

#### 순환 대기(Circular wait)
- 프로세스의 집합(P0, P1, ... Pn)에서 P0는 P1이 점유한 자원을 대기하고 P1과 P2가 점유한 자원을 대기하고 P2...Pn-1은 Pn이 점유한 자원을 대기하며 Pn은 P0가 점유한 자원을 요구해야함

-------

### 데드락(DeadLock) 처리
- 3가지로 분류할 수 있음

- 데드락이 발생하지 않도록 예방(prevention)하기

- 데드락 발생 가능성을 인정하면서도 적절하게 회피(avoidance)하기

- 데드락 발생을 허용하지만 데드락을 탐지(detection)하여, 데드락에서 회복하기

--------

### 데드락 예방(Prevention)
- 교착 상태 발생 조건 중 하나를 제거함으로써 해결하는 방법

- 단, 이 방식은 시스템의 처리량이나 효율성을 떨어트리는 단점이 발생할 수 있음, 자원의 낭비가 심함

#### 상호 배제(Mutual Exclusion) 부정
- 한 번에 여러 개의 프로세스가 공유 자원을 사용할 수 있도록 함(동기화 문제가 발생할 수 있기는 함)

#### 점유 대기(Hold and wait) 부정
- 프로세스가 실행되기 전 필요한 모든 자원을 할당함 즉, 프로세스 실행에 필요한 모든 자원을 한꺼번에 요구하고 허용될 때까지 작업을 보류해서, 나중에 또다른 자원을 점유하기 위한 대기 조건을 성립하지 않도록 함

#### 비선점(No preemption) 부정
- 자원을 점유하고 있는 프로세스가 다른 자원을 요구할 때 점유하고 있는 자원을 반납하고, 요구한 자원을 사용하기 위해 기다림(높은 우선순위의 프로세스가 해당 자원을 선점할 수 있도록 함)

#### 순환 대기(Circular wait) 부정
- 자원에 고유한 번호를 할당하고, 번호 순서대로 자원을 요구하도록 함(순환 형태의 대기가 아닌 일정한 한 쪽 방향으로만 자원을 요구할 수 있도록 함)

--------

### 데드락 회피(Avoidance)
- 회피를 보기 앞서 먼저 시스템의 프로세스들이 요청하는 모든 자원을, 데드락을 발생시키지 않으면서도 차례로 모두에게 할당해 줄 수 있다면 안정 상태(safe state)에 있다고 말함

- 그리고 이처럼 특정한 순서로 프로세스들에게 자원을 할당, 실행 및 종료 등의 작업을 할 때 데드락이 발생하지 않는 순서를 찾을 수 있다면, 그것을 안전 순서(safe sequence)라고 부름

- 반면 불안정 상태는 안정 상태가 아닌 상황을 말함, 데드락 발생 가능성이 있는 상황이며 교착 상태(데드락)는 불안정 상태일 때 발생할 수 있음(교착 상태가 불안정 상태의 부분집합)

- 여기서 데드락 회피, 그리고 회피 알고리즘은 자원을 할당한 후에도 시스템이 항상 safe state에 있을 수 있도록 할당을 허용하는 것이 기본 특징임

#### 은행원 알고리즘(Banker's Algorithm)
- 은행에서 모든 고객의 요구가 충족되도록 현금을 할당하는 데서 유래한 기법으로 프로세스가 자원을 요구할 때 시스템은 자원을 할당한 후에도 안정 상태로 남아있게 되는지를 사전에 검사하여 교착 상태를 회피하는 기법

- 안정 상태에 있으면 자원을 할당하고, 그렇지 않으면 다른 프로세스들이 자원을 해지할 때까지 대기함

- 교착 상태가 되지 않도록 보장하기 위하여 교착 상태를 예방하거나 회피하는 프로토콜을 이용하는 방법임

- 즉, 어떤 자원의 할당을 허용하는지에 관한 여부를 결정하기 전에 미리 결정된 모든 자원들의 최대 가능한 할당량을 가지고 시뮬레이션 해서 Safe State에 들 수 있는지 여부를 검사함(그래서 대기중이 다른 프로세스들의 활동에 대한 교착 상태 가능성을 미리 조사하는 것임)

- 하지만 이런 은행원 알고리즘도 미리 최대 자원 요구량을 알아야 하고, 할당할 수 있는 자원 수가 일정 해야 하는 등 사용에 있어 제약조건이 많음, 그에 따른 자원 이용도 하락 등 단점도 있음

-------

### 데드락 탐지(Detection) 및 회복(Recovery)
- 데드락 예방이나 회피법을 사용하지 않았을 때, 데드락이 발생할 수 있으니 여기에서 회복하기 위해 데드락을 탐지하고, 회복하는 알고리즘을 사용함

#### 탐지 기법
- Allocation, Request, Available 등으로 시스템에 데드락이 발생했는지 여부를 탐색함(은행원 알고리즘에서 했던 방식과 유사하게 현재 시스템의 자원 할당 상태를 가지고 파악함)

- 이 외에도 자원 할당 탐지 그래프를 통해 탐지하기도 함

#### 회복 기법
- 데드락을 탐지 기법을 통해 발견했다면, 순환 대기에서 벗어나 데드락으로부터 회복하기 위한 방법을 사용함

- 교착 상태를 일으킨 프로세스를 종료하거나, 할당된 자원을 해제함으로써 회복하는 것을 의미함

##### 프로세스 종료
- 아래와 같은 프로세스 종료 방식으로 교착상태로부터 회복할 수 있음

- 교착 상태의 빠진 모든 프로세스를 중단시키는 방법 : 계속 연산중이던 프로세스들도 모두 일시에 중단되어 부분 결과가 폐기될 수 있는 부작용이 발생할 수 있음

- 프로세스를 하나씩 중단 시킬 때마다 탐지 알고리즘으로 데드락을 탐지하면서 회복시키는 방법 : 매번 탐지 알고리즘을 호출 및 수행해야 하므로 부담이 되는 작업일 수 있음

##### 자원 선점하기
- 프로세스에 할당된 자원을 선점해서, 교착 상태를 해결할 때까지 그 자원을 다른 프로세스에 할당해 주는 방법(우선 순위가 낮은, 수행한 횟수가 적은 프로세스 등을 위주로 프로세스의 자원을 선점함)
