## 중앙처리장치(CPU)
- Central Processing Unit로 컴퓨터 시스템에 부착된 몬든 장치의 동작을 제어하고 명령을 실행하는 장치임

![one](/cheewr85/img/computerarchitecture/thirteen.png)

- 위와 같은 구조로 이루어졌다고 이해하면 좋음, 그러면 이를 하나씩 알아보면

-----

### 제어장치(Control Unit)
- 제어장치는 컴퓨터에 있는 모든 장치들의 동작을 지시하고 제어하는 장치임, 명령 레지스터에서 읽어들인 명령어를 해독하여 해당하는 장치에게 제어 신호를 보내 정확하게 수행하도록 지시함

- 명령어의 실행(Instruction Cycle) 단계는 인출, 간접, 실행, 인터럽트 단계로 나눌 수 있음

#### 인출(Fetch)
- 메모리에서 데이터를 로드하여 CPU에 있는 레지스터에 적재하는 과정

#### 간접(Indirect)
- 메모리를 참조할 때 간접주소 방식을 사용하는 경우에 실행

- 간접주소란 CPU가 메모리를 참조했을 때 데이터가 존재하는 것이 아니라 메모리에 주소가 존재하여 메모리 내에서 한 번 더 조회해서 데이터를 얻는 것

#### 실행(Execute)
- 명령과 데이터로 CPU가 산술 및 논리연산을 수행하는 것

#### 인터럽트(Interrupt)
- 컴퓨터 작동 중 예기치 않은 문제 발생 시 업무 처리가 계속 될 수 있도록 하는 컴퓨터 운영체제의 한 기능으로, 크게 하드웨어 인터럽트, 소프트웨어 인터럽트로 나뉨

- 하드웨어 인터럽트 : 기계착오 인터럽트, 외부 인터럽트, 입출력 인터럽트, 프로그램 검사 인터럽트

- 소프트웨어 인터럽트 : CPU 내부에서 자신이 실행한 명령이나 CPU의 명령 실행에 관련된 모듈이 변환하는 경우 발생

-----

### 연산장치(ALU; Arithmetic & Logic Unit)
- 제어장치의 명령에 따라 실제로 연산을 수행하는 장치임

- 연산장치가 수행하는 연산에는 산술연산, 논리연산, 관계연산, 이동(Shift)등이 있음

- 연산장치는 가산기, 누산기(AC; Accumulator), 보수기, 데이터 레지스터, 오버플로 검출기, 시프트 레지스터 등으로 구성

-----

### 레지스터(Register)
- CPU 내부에서 처리할 명령어나 연산의 결과나 주소 등을 일시적으로 기억하는 임시 기억 장소

- 메모리 중에서 속도가 가장 빠름

- 레지스터에 새로운 데이터가 전송되면 기존에 있던 내용은 지워지고 새로운 내용만 기억

- 레지스터는 연산 속도를 향상하기 위해 사용

- 아래와 같이 여러가지 레지스터의 종류가 존재함

#### 프로그램 카운터(PC; Program Counter)
- 다음에 수행할 명령어가 저장된 주기억장치의 번지를 지정하는 레지스터

#### 명령 레지스터(IR; Instruction Register)
- 현재 실행중인 명령의 내용을 기억하는 레지스터

#### 누산기(AC; Accumulator)
- 연산된 결과를 일시적으로 저장하는 레지스터로 연산의 중심이 됨

#### 상태 레지스터(Status Register)
- Program Status World Register, 플래그 레지스터(Flag Register)라고도 함

- 시스템 내부의 순간 순간의 상태가 기록된 정보를 PSW라고 함

- 오버플로, 언더플로, 자리올림, 인터럽트 등의 PSW를 저장하고 있는 레지스터

- 제어장치와 연산장치의 실행 순서를 제어하기 위해 사용되는 레지스터

#### 메모리 주소 레지스터(Memory Address Register)
- 기억장치를 출입하는 데이터의 번지를 기억하는 레지스터

#### 메모리 버퍼 레지스터(Memory Buffer Register)
- 기억장치를 출입하는 데이터가 잠시 기억되는 레지스터

- 버퍼 레지스터라고도 함

#### 인덱스 레지스터(Index Register)
- 주소의 변경, 서브루틴 연결 및 프로그램에서의 반복 연산의 횟수를 세는 레지스터

#### 데이터 레지스터(Data Register)
- 연산에 사용될 데이터를 기억하는 레지스터

#### Shift Reigster
- 클럭 펄스(Clock Pulse)에 의해서 기억된 내용을 왼쪽 또는 오른쪽으로 1bit씩 자리를 이동시키는 레지스터(2배 길이 레지스터라고도 함)

------

### 버스
- CPU, 메모리, I/O 장치 등과 상호 필요한 정보를 교환하기 위해 연결된 공동의 전송선

- 버스를 통해 전달되는 제어 신호, 주소 싷노 및 데이터 신호의 상호 시간적 관계가 유지됨

#### 번지 버스(Address Bus)
- CPU가 메모리나 입출력기기의 번지를 지정할 때 사용하는 단방향 전송선

#### 자료 버스(Data Bus)
- CPU와 메모리 또는 입출력기기 사이에서 데이터를 전송하는 양방향 전송선

#### 제어 버스(Control Bus)
- CPU의 현재 상태나 상태 변경을 메모리 또는 입출력장치에 알리는 제어신호를 전송하는데 사용하는 양방향 전송선

#### 내부 버스
- CPU 및 메모리 내에 구성된 Bus

#### 외부 버스
- 주변 입출력 장치에 구성된 Bus

-------

##### 참고자료
https://coding-factory.tistory.com/351
https://cozy-dandelion.tistory.com/7