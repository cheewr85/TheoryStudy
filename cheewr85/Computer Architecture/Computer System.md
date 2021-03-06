## 컴퓨터 시스템
- 일반적으로 크게 2가지로 바라 볼 수 있음, 이를 간단하게 알아본다면 

- 먼저 시스템을 구성하는 물리적 부분으로 이루어진 장치로 하드웨어를 말함, CPU, Memory, HDD, Keyboard등의 장치들을 말함

- 그리고 컴퓨터를 작동시키고 제어하고 이를 관리하는 용도의 소프트웨어가 존재함

- 여기서 구체적으로 각 나뉘어진 부분별로 하나씩 세부적으로 볼 수 있음

---------

### 하드웨어 
- 이 부분은 앞서 봤던 부분의 구조를 참조하여서 하나씩 알아볼 것임
![one](/cheewr85/img/computerarchitecture/one.png)

- 하드웨어는 일반적으로 위와 같은 구조를 가지고 있고 여기서 위의 구조를 활용하여 하나씩 알아본다면

#### CPU
- 여기서 CPU는 연산을 수행하는 산술논리장치(ALU), 제어 명령을 전달하는 제어장치(CU), 결과값을 일시적으로 기억하는 레지스터(Register)로 구성되어 있음

- 여기서 조금 더 알아보면 제어장치의 경우 메인 메모리로부터 명령어를 인출 및 해독해 연결된 장치에 제어 신호를 보냄

- 그리고 산술논리장치의 경우 제어 신호에 따라 명령어가 의도하는 대로 데이터를 실행하기 위해 CPU 내부에서 처리하는 장치임(연산을 하는 것, 연산 장치라고도 함)

- 레지스터는 이런 데이터 처리하는 동안 사용할 값, 연산의 중간 결과를 일시적으로 기억하는 것임(세부적으로 보면 명령어 레지스터, 누산기등이 존재함, 이 세부적인 부분은 CPU 작동원리에서 알아볼 것)

- 그리고 CPU 내부 버스로 제어 및 연산장치, 레지스터 등과 같은 CPU 내부 구성요소를 연결하는 배선의 집합임, 데이터 신호, 주소 신호, 제어 신호등을 전송함

#### Memory
- CPU에는 Register가 존재하여 데이터를 읽어와서 명령을 처리하고 속도가 빠른 장점은 있지만 용량은 매우 작음

- 그래서 이러한 정보를 저장해 두었다가 필요할 때 읽어 들여 이용할 수 있는 주기억장치가 필요함, 그래서 이 Main Memory가 그 역할을 함

- 주기억장치는 컴퓨터가 켜지면 운영체제, 사용자 프로그램등이 메모리공간에 올라가게되고 CPU는 주기억장치에서 프로그램들의 명령어등을 읽어와 작업을 수행함

- 주로 RAM, ROM등을 일컫음

#### 기억장치
- 우선 Memory만 별도로 본 것은 CPU에서 수행할 프로그램과 필요한 데이터 그리고 앞서 말했듯이 프로그램과 데이터를 저장하기 때문에 미리 말함

- 하지만 이 Main Memory말고도 기억장치로써 Disk도 존재하고 Register, Cache Memory, Main memory등으로 구성되고 그 구성 관계가 계층적으로 이루어져 있음
![one](/cheewr85/img/computerarchitecture/seven.png)


#### 시스템 버스
- 위에서 말한 요소들을 말고도 다양한 구성 요소에 대해서 서로 연결하기 위한 버스가 존재함, 이 버스를 통해서 각종 신호를 전달 및 교환을 함

- 그래서 그림에서 보이는 바와 같이 시스템 버스, I/O 버스, 메모리 버스등이 각 구성요소를 연결해준다고 볼 수 있음

- 그리고 여러가지로 구분을 할 수 있는데 데이터 버스의 경우 CPU, 메인 메모리, 주변장치 사이에 데이터를 전송하는 역할로 배선의 수에 따라 한 번에 전송할 수 있는 비트 수를 결정함

- 주소 버스는 CPU가 시스템 구성 요소를 식별하기 위한 주소 정보를 전송하는 배선의 집합, 배선의 수는 CPU와 접속할 수 있는 최대 메모리의 용량을 결정함

- 제어 버스는 CPU가 시스템 구성 요소의 동작을 제어하는데 사용되는 배선의 집합임

- 그리고 직관적으로 이런 버스들이 맨 처음 봤던 구조에서 보이는 것처럼 각각의 CPU와 I/O bridge, 여러 I/O 장치를 연결해주는 역할을 해 줌

#### 메인보드
- 여기서 맨처음 구조도와 같이 되어 있는데 있어서 메인보드에 이런 부품들과 관련하여 연결하고 꽂아서 처리를 할 수 있음

- 즉, 각각의 CPU, Memory, controller, adapter 등이 여기에 꽂혀있음

#### Device Controller
- 그림에서 USB Controller, Disk Controller들로써 각각의 디바이스들을 I/O연산, 제어관리 하는 역할을 함

- 이들이 각각 Device Driver 즉, 운영체제가 각각의 디바이스를 접근하게 하기위한 소프트웨어가 매개를 하고 각각의 펌웨어가 존재, 디바이스가 동작하기 위한 소프트웨어가 있음

- 그리고 이를 Buffer를 통해서 각각의 Device Controller들이 명령을 내려 Device의 데이터를 읽어오거나 출력할 때 저장하는 공간이 있음

- 이러한 구조가 얽혀서 Device들의 입출력이 이루어짐

- 추가적으로 어댑터도 이러한 역할을 한다고 볼 수 있음, 그래픽 어댑터는 즉, 이 어댑터로 보여지는 이미지와 비디오를 그려서 모니터로 보여주는 것임

##### 일반적인 구조는 위와 같고 이 과정들이 서로 내부적으로 복잡하게 얽혀 작동을 하는 과정이 있음

---------

### 소프트웨어 
- 소프트웨어는 명령어의 집합으로 데이터의 이동 방향과 데이터 처리의 종류를 지정하고, 이벤트 발생 시간을 지정하는 명령의 집합 자체임

- 이를 크게 시스템 소프트웨어, 응용 소프트웨어로 볼 수 있음

- 그래서 동작을 보면 `사용자 <-> 응용 소프트웨어 <-> 시스템 소프트웨어 <-> 하드웨어`로 동작한다고 볼 수 있음

#### 시스템 소프트웨어
- 하드웨어 기능을 수행하기 위한 필수적인 명령어 해석, 하드에 데이터 저장, 주변장치와 통신을 하거나 응용 소프트웨어의 실행을 지원하는 소프트웨어임

- 여기서도 세분화 되어 있는데 먼저 운영체제(Operating System)의 경우 컴퓨터 자원을 관리, 응용 소프트웨어의 실행 환경을 제공함

- 장치 드라이버(Device Driver)는 컴퓨터에 연결되는 주변 기기를 제어하는 것임

- 컴파일러(Compiler)와 인터프리터(Interpreter)는 고급언어로 작성된 소스 프로그램을 컴퓨터가 이해하는 저수준언어로 변환하는 일종의 번역기 역할을 함

- 링커(Linker)는 이렇게 작성된 프로그램에 의해 생성된 프로그램(Object program) 또는 라이브러리 루틴을 결합해 실행 가능한 하나의 프로그램으로 연결을 함

- 로더(Loader)는 저장장치에 보관된 프로그램을 읽어 메인 메모리에 적재한 후 실행 가능 상태로 만듦

- 이 과정을 정리해보면 `고수준 언어 프로그램 -> 번역기(컴파일러, 인터프리터) -> Object 프로그램(기계어 모듈) -> 링커 -> 실행 프로그램 -> 로더 -> 메모리`로 처리됨

#### 응용 소프트웨어
- 컴퓨터 시스템을 특정 분야에 사용하기 위해 제작된 소프트웨어, 애플리케이션, 앱, 어플이라고도 함 

- 예를 들어서 스프레드 시트, 워드 프로세스등 우리가 흔히 썼던 프로그램들이 이에 속함