# CPU, Instruction Fetch & Execute
## CPU의 구성 요소
- Address side 레지스터와 data side 레지스터
- Arithmetic logic unit
- Control session & Control unit
- 메모리와 연결시켜주는 단방향 address 버스와 양방향 data 버스
- 한 레지스터의 출력을 다른 레지스터의 입력으로 연결하는 내부 버스와 데이터 경로
![Bog Standard Architecture](/sejigner/img/systems_1.4/cpu/1.png)

### CPU 레지스터
- Memory Address Register : 메모리를 접근하기 위해 필요한 주소값을 저장한다.
- Memory Buffer Register : 양방향 데이터 버스를 통해  메모리와 주고받을 데이터를 저장한다.
- Accumulator: ALU에서 작업할 데이터를 저장한다. CPU의 데이터 부분에서 핵심적이다. 메모리는 Accumulator에 직접 접근하지 못하고, Memory Buffer Register가 중재자 역할을 한다.
- Program Counter : internal address bus에 연결되어 메모리 속에 있는 다음 프로그램의 명령어 값을 가리킨다.  메모리에 직접 접촉하지 않고, memory address register를 한번 거친다.
- Instruction Register : 메모리가 읽힐 때 데이터가 우선 memory buffer register로 이동한다. 데이터가 명령어이면 Instruction Register로 옮겨진다. IR은 opcode와 address IR로 구분된다.
	- IR(opcode) : 가장 핵심적인 명령어가 opcode를 구성한다. Control Unit에 의해 디코드 되고 실행된다.
	- IR(address) : 핵심적이지 않은 명령어들은 IR (adress)로 보내진다. Memory Address Register에서 쓰이게 될 주소의 일부 또는 전체를 지닌다.
	- Stack Pointer : internal address bus에 연결되어 프로그램 실행 중에 메인 메모리가 일시적인 저장소로 사용되는 특별한 부분의 주소값을 갖는다.

### CPU 유닛
- Control Unit : 명령어 레지스터에 있는 명령어를 수행하는데 필요한 다양한 레지스터 전송의 실행과 그 시점을 담당한다. 
- ALU : Accumulator와 Memory buffer register에 저장된 데이터의 비트 작업과 그 작업 결과의 저장을 담당한다.
- Status Register(Condition Control) : 조건 혹은 이벤트를 검사하여 프로그램 실행 순서를 변경할지 결정하기 위하여 참조한다. 단지 ALU가 산출한 결과를 가리키는 1비트 플래그의 모음일 뿐이라는 점에서 다른 레지스터에 비해 활용이 제한적이다. 플래그는 Carry C, Overflow V, Negative flag N, zero flag Z 등이 있는데, true 또는 false 값을 갖는다. 

### Bus와 Register의 넓이
- 데이터 : 마이크로컨트롤러는 4 비트, 8 비트, 16 비트, 32 비트, 64 비트 넓이의 데이터 버스를 갖는다. 

![Bog Standard Architecture](/sejigner/img/systems_1.4/cpu/2.jpg)

데이터 버스는 2 바이트 또는 16 비트 넓이이다. 
- 주소 : 주소 버스는 데이터 버스의 넓이와 같을 필요 없다. n개의 주소 라인을 갖는다는 것은 2^n개의 주소 또는 그 주소 공간의 location이 존재한다는 것을 의미한다. n = 10일 경우, 1K locations, n=20 -> 1M, n=30 -> 1G이다. 그러나 마이크로컨트롤러는 멀티 태스킹 용도로 디자인된 것이 아니어서 그렇게 많을 필요가 없다.
- Instruction Register의 opcode(연산코드) 파트는 opcode의 가능한 최댓값만큼 커야한다. 8 bits width라고 가정하면, 256개의 다른 명령어가 가능해지는데, IR은 이만큼의 크기를 갖추고 있어야 한다. IR의 address 파트는 address 버스의 크기와 같아야 한다. 

![Bog Standard Architecture](/sejigner/img/systems_1.4/cpu/3.jpg)

여기서 opcode IR 8 bits + address IR 24 bits -> 전체 IR은 32 bit의 width를 갖는다.

### 메인 메모리
메모리는 거의 RAM이 대부분을 차지하고, 약간의 추가 읽기전용 메모리 ROM으로 구성된다, 메인 메모리는 CPU 칩 이에 있지는 않지만, 마더보드 위에서 버스(데이터 버스+주소 버스)를 통해 CPU로 연결된다.

![Bog Standard Architecture](/sejigner/img/systems_1.4/cpu/4.jpg)

주소 버스가 24 비트 width니까, 주소 공간은 0x0 부터 2^24 -1 까지 할당된다. 데이터 버스는 16 비트 width로, 내용물의 너비가 16 비트이다. 따라서 데이터 버스가 취할 수 있는 가장 큰 값은 2의 16승 -1 또는 oxFFFF이다.

## Fetch, Decode, Execute 사이클
CPU는 다음의 사이클을 반복적으로 수행한다.
- Fetch : 다음 명령어를 메모리로부터 받아서 instruction register에 fetch
- Decode : 명령어를 decode
- Execute

명령어를 fetch하고 execute하려면, CPU의 Control Section이 Level과 Pulse를 통해 경로를 결정하고 레지스터 전송해야 한다. 그러면 데이터가 메모리에서 레지스터로, 또는 레지스터 사이에서 이동하고, 필요할 경우 데이터가 ALU를 통과하며 메모리로 돌아온다.

### 명령어 Fetch와 Decode
cpu는 먼저 메인 메모리로부터 명령어를 불러온다. 이때, PC는 핵심적인 레지스터로, 메인 메모리에 있는 다음 프로그램 명령어의 주소를 지닌다. 그러고 나서 PC가 Memory address register로 복사된다. 그리고 Memory buffer register가 그것을 읽는다. 이제 Instruction register가 MBR 속의 명령어를 복사한다. Opcode IR에서 opcode(연산코드)가 control unit에 의해 해독된다. 마지막으로 PC의 값이 1 증가한다. 
### 명령어 Execution
명령어를 불러오는 동안 연산코드는 Opcode IR에 들어가고, Control Unit에 의해 해독된다. 이제 CU는 어떤 명령어를 수행할지 알고, 경로 설정과 원하는 레지스터 전송을 위해 일련의 Level과 Pulse를 산출해낸다. 

> Written with [StackEdit](https://stackedit.io/).
