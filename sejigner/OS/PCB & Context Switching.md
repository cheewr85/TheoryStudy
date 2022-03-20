# PCB & Context Switching
PCB와 Context Switching은 여러 개의 프로세스가 돌아갈 때 CPU 스케줄링을 통해 관리하는데 사용된다. 이때 프로세스에 대한 전반적인 정보를 가지고 있는 것이 **Process Metadata**이다.
## Metadata
- Process ID: PID(Process Identification Number) 프로세스 고유 식별 번호
- Process State(프로세스 상태): 프로세스의 현재 상태(준비, 실행, 대기 상태)
- Process Priority(스케줄링 정보): 프로세스 우선순위 등과 같은 스케줄링 관련 정보 기억
- CPU Registers: 프로세스의 여러 레지스터의 상태와 보유 정보를 저장
- Owner(계정 정보): CPU 사용시간의 정보(Quantum), 각종 스케줄러에 필요한 정보를 기억
- 기억장치 관리 정보: 프로그램이 적재될 기억 장치의 상한치, 하한치, 페이지 테이블 등의 정보 기억
- 입출력 정보: 프로세스 수행 시 필요한 주변 장치, 파일들의 정보
- 프로그램 카운터(계수기): 다음에 실행되는 명령어의 주소 기억
## PCB(Process Control Block)
프로세스의 메타데이터가 저장되는 곳으로 하나의 PCB에 하나의 프로세스 메타데이터가 담김
프로세스가 생성되면 Metadata가 PCB에 저장된다.
![1](/img/os/pcb & context switching/1.jpg)
- Process State : 프로세스의 상태 (생성, 준비, 실행, 중단, 종료)
- Process Counter : 다음으로 실행할 명령어의 주소

### PCB의 용도
- CPU 하나의 코어에서는 하나의 프로세스만 실행할 수 있다. 
- 생성되고 준비된 상태인 프로세스가 CPU의 제어권을 가져오기 위해서 인터럽트를 발생시키면, 원래 실행 중이던 프로세스는 중단(Block) 상태가 되고 새로운 프로세스가 실행(running) 상태가 된다. 
- 중단된 프로세스는 PCB에 중단 시점의 정보를 PCB에 저장해 둔다.
### PCB 관리
- PCB List Head에 PCB가 생성이 된다. 
- 각각의 블럭이 주솟값으로 연결되어 있는 Linked List 방식으로, 삽입과 삭제가 용이하다.
- 수행하고 있는 프로세스를 멈추고 새로운 프로세스를 실행할 때, CPU의 레지스터 정보가 변경되는데 이것이 Context Switching이다.
### Context Switching
- CPU의 코어 하나가 한번에 하나의 Task만 처리할 수 있다는 한계를 극복하기 위해, 빠른 속도로 여러 프로세스를 번갈아가며 수행하게 된다.
- 이를 통해 컴퓨터가 동시에 여러 Task를 수행하는 것처럼 보이게 하고, 병행성(Concurrency)를 확보한다.
- 실행 중이던 프로세스의 메타데이터를 PCB에 보관하고, 이번에 실행할 프로세스의 정보를 PCB로부터 가져와서 레지스터에 담는다.
- 인터럽트가 발생하면 실행 중인 프로세스의 상태 정보를 저장하고, 제어권이 인터럽트 서비스 루틴(ISR)에 넘어가는 작업이다.
### Context Switching Cost
Context Switching이 발생하면 다음과 같은 비용이 발생한다.
1. Cache 초기화
2. 메모리 매핑 초기화

즉, 잦은 Context Switching은 성능 저하로 이어짐
### 시간할당량
- 시간 할당량은 멀티프로그래밍에서 한 프로세스가 수행될 시간을 의미함
- 할당량이 적으면 여러 개의 프로세스가 동시에 돌아가고 있다는 느낌을 주지만 인터럽트의 수가 많아지고 Context Switching 역시 잦아진다.
- 프로세스 실행에 필요한 부가적 활동을 Overhead라고 통칭하는데, 이 역시 증가한다.


### Reference : https://m.blog.naver.com/adamdoha/222019884898

> Written with [StackEdit](https://stackedit.io/).

