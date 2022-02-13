## 컴퓨터 구조 기초
- 가장 흔히 볼 수 있는 예시로 단순하기 hello.c source program을 만들었다면 compliation system을 통해서 hello라는 실행가능한 object 파일로써 disk에 저장됨

- 이 실행가능한 파일을 Shell이라는 곳에서 명령어 입력을 해석해서 해당 명령을 수행하고 처리할 수 있게함, 여기서 리눅스 기준 `./hello`를 하면 실행가능한 파일로 보고 load가 되고 run이 됨

- 그러면 hello 프로그램은 실행이되서 hello, world를 출력하고 사라짐, 그리고 shell은 사라질때까지 기다리고 다음 명령어 입력을 기다리는 식으로 진행이 됨

---------------

### 시스템 하드웨어 구성
![one](/cheewr85/img/computerarchitecture/one.png)

- 이처럼 hello 프로그램이 실행되는 과정을 이해하려면 일반적인 시스템에서의 하드웨어 구성이 위와 같다는 것을 이해해야함

- 위의 시스템은 Intel 기준이지만 대부분의 시스템이 이와 유사한 구조를 가지고 있음

#### Buses
- 시스템에서 실행시 buses라 불리는 집합체를 통해서 정보의 bytes가 컴포넌트 사이에서 주고받아짐

- 버스는 words라고 불리는 bytes의 고정된 사이즈의 묶음을 전송하게끔 디자인되어 있음, 이 word의 bytes는 시스템마다 다양하게 구성됨

- 일반적으로 오늘날은 word의 크기는 4 bytes 혹은 8 bytes임, 우선 다른 사이즈는 제외함

#### I/O Devices
- I/O Devices는 외부와 연결할 수 있는 장치를 말함

- 일반적으로 입력을 받는 마우스와 키보드, 출력을 보여주는 디스플레이, 디스크 드라이브가 데이터와 프로그램을 저장하고 있음, 위에서 hello 프로그램이 디스크에 있는 것임

- 이 I/O Device는 I/O bus에 controller와 adapter로 연결되어 있음, controller의 경우 그 자체 기기의 칩셋 혹은 시스템의 마더보드에 있는 것이고 adapter는 마더보드에 연결되어 있는 것임, 그럼에도 이 2개의 역할은 I/O bus와 I/O Device 간의 정보전달을 목표로함


#### Main Memory
- Main Memory는 프로세스가 프로그램을 실행하고 있는 동안 프로그램과 데이터를 가지고 있는 임시 저장소임

- 물리적으로 DRAM 칩의 집합체로 구성되어 있고 논리적으로는 0부터 시작하는 각각의 고유한 주소가 있는 linear array of bytes로 이루어짐

- 일반적으로 이런 machine instructions은 다양한 bytes로 구성할 수 있는 프로그램으로 구성되어 있음 즉, 이러한 데이터의 경우 프로그램의 타입에 따라서 매우 다양함

#### Processor
- CPU 혹은 Processor라고 부르는데 주로 main memory에 저장되어 있는 명령을 해석하고 실행하는 엔진임

- 내부에는 word-size 저장소(레지스터)인 Program Counter(PC)가 있음

- 어떤 상황이든 PC는 main memory에 있는 기계어 명령을 가리킴(주소로 구성된)

- 전윈이 켜지고 꺼질 때까지 프로세스는 PC가 가르키고 있는 명령을 반복적으로 수행하고 PC가 다음 명령을 가르키도록 업데이트를 함

- 이 프로세스는 상당히 단순한 명령어 실행 모델을 나타냄, 이 모델에서는 명령이 정해진 순서대로 철저하게 실행이 됨

- PC가 메모리로부터 가르킨 명령을 읽고 명령어의 비트를 해석하며 명령어에 의해 쓰여진 단순한 실행을 수행하고 PC를 다음 명령어로 업데이트를 함(메모리가 인접하던 안하던 명령어를 단순히 실행하는 것)

- 이러한 실행은 main memory, register file, arithmetic/logic unit(ALU)를 계속 돌아가면서 수행이 됨

- register file은 각각의 고유한 값이 있는 word-size register의 집합체로 구성된 작은 저장 장치임, ALU는 새로운 데이터와 주소 값을 계산함

- CPU는 몇 가지 명령어의 요청을 수행함, 아래와 같이 예시를 볼 수 있음
![one](/cheewr85/img/computerarchitecture/two.png)

- 위와 같은 과정이 일반적으로 processor에서 수행된다고 볼 수 있지만 현대 processor는 프로그램 실행의 속도를 높이기 위해서 더 복잡한 로직으로 돌아감

#### hello 프로그램 실행 과정
- 앞서 맨처음 설명한 hello 프로그램의 과정을 이 구조로 풀어서 말할 수 있음
![one](/cheewr85/img/computerarchitecture/three.png)

- Shell이 명령어 입력을 기다리고 한 글자씩 키보드에 입력 받을때마다 shell이 그것을 register에서 읽고 memory의 그림과 같이 저장을 함, 그리고 엔터를 치면 shell이 명령 입력이 끝난 것을 인식함, 그때 실행가능한 hello 파일을 저장해서 코드를 복사해 명령 과정을 수행하고 hello 파일에 데이터를 disk로부터 main memory로 복사함, 여기서 이 데이터에는 `hello, world\n` 가 포함됨

- 여기서 DMA라는 기법을 활용해서 데이터가 disk에서 바로 main memory로 processor를 지나지 않고 아래와 같이 접근을 할 수 있음
![one](/cheewr85/img/computerarchitecture/four.png)

- 그리고 코드와 hello 객체 파일의 데이터가 memory로 저장이 되면 processor는 hello program의 main에 있는 기계어 명령을 수행을 시작을 함

- 이 명령어는 memory에서 register file로 `hello world\n`가 복사가 되고 거기서 아래와 같이 출력이 보이게 됨
![one](/cheewr85/img/computerarchitecture/five.png)

-------------------

### 캐시
- 위에서 예제에서 알 수 있는 부분은 시스템은 많은 시간을 정보를 하나의 곳에서 다른 곳으로 옮기는데 쓴다는 것임

- hello program의 명령어는 원래 disk에 저장되어 있지만, 프로그램이 load 될 때, main memory로 복사됨, processor가 프로그램을 실행하면 명령어는 main memory에서 processor로 복사가 됨

- 이와 유사하게 `hello,world\n` 데이터는 처음엔 disk에 있었지만 main memory로 복사되고 그리고 display device까지 복사가 됨

- 프로그래머 관점에서 이런 복사가 실제 프로그램 작업을 늦춰지는 것을 야기할 수 있음을 생각할 수 있음 

- 따라서 시스템 디자이너는 이러한 복사를 가능한 빨리 수행하도록 설계를 함

- 물리적인 이유로 크기가 큰 저장장치는 작은 저장장치보다 느리고 빠른 장치들은 느린 장치보다 설계비용이 더 들어감, disk drive의 경우 main memory보다 1000배 가량 크지만, processor가 memory로부터 word를 읽어 오는 것보다 10000000배 더 길게 걸린다

- 이와 유사하게 register file은 main memory는 110억 바이트 정도 가진 것에 비해 100바이트 정도만 가지고 있음, 하지만 processor는 register file로부터 memory보다 100배 정도 빠르게 읽어올 수 있음

- 이러한 processor-memory의 차이는 지속적으로 증가하였고 main memory를 빠르게 만드는 것보다 processors를 빠르게 만드는데 더 쉽고 비용을 저렴하게 할 수 있었음

- 이런 문제를 처리하기 위해서 더 작고 빠른 저장 장치인 cach memories(cache라고도 부른)를 통해 processor가 가까운 시간안에 정보를 필요로하는 상황에서 임시적으로 저장하는 곳을 만듬, 그림으로 보면 아래와 같음
![one](/cheewr85/img/computerarchitecture/six.png)

- 이 캐시 구조는 아래와 같이 계층적으로 이루어져 있음
![one](/cheewr85/img/computerarchitecture/seven.png)

- processor chip에 있는 L1 cache는 register file정도로 빠르게 접근할 수 있고 수십, 수천개의 bytes를 가지고 있고 L2 cache는 수백만 bytes까지 가능하며 special bus에 의해 processor와 연결되어 있고, L1 Cache보다 접근하는데 5배 정도 더 걸림, 하지만 이건 main memory에 접근하는 것보다는 5 ~ 10배는 빠름

- 이 L1, L2 Cache는 SRAM이라는 기술이 적용되어 있음

- 더 강력한 시스템은 이 계층이 3개로 되어 있음 L1, L2, L3으로

- 이 cache의 아이디어는 큰 memory와 빠른 속도에 대한 특징을 동시에 효과를 볼 수 있게 함, localized된 곳에서 데이터와 코드를 접근하게 할 수 있기 때문에

- cache가 자주 접근해서 data를 가지고 있으면 빠른 cache를 활용해서 memory 기능을 더 확실하게 활용할 수 있음

- 이를 통해서 프로그램의 성능을 더 끌어올릴 수 있음

-----------------

### 메모리 계층구조
![one](/cheewr85/img/computerarchitecture/seven.png)

- 위와 같이 계층적인 구조를 가진 것이 모든 컴퓨터 시스템에서의 저장 장치가 다루는 일반적인 아이디어임

- bottom으로 갈수록 느려지고 커치고 per byte 비용이 줄어들고 top으로 가면 그 반대로 됨

- 기본 개념은 상위 계층이 밑의 하위 계층의 캐시 역할을 한다는 것임, 여기서 만약 네트워크 환경의 분산 시스템일 경우 local disk가 다른 시스템 disk의 캐시 역할을 할 수 있음, 이를 통해서 성능에 대한 사항을 생각해 볼 수 있음

-------------------

### The Operating System Manages the Hardware
- 계속 다루고 있는 예제로 hello program이 load되고 실행되는 과정에서 OS가 제공하는 서비스에 의존을 함
![one](/cheewr85/img/computerarchitecture/eight.png)

- 이 Opearting System은 위와 같이 Application Program과 Hardware 사이에 위치해 있음 즉 Application Program이 Hardware를 조작하는 모든 시도는 Operating System을 거치게 됨

- Opearting System은 실행중인 application이 hardware를 오용하는 것을 보호하고 저수준의 hardware를 다룰 때 복잡하고 완전히 다른 것을 조작을 할 때 간단하고 규격화된 매커니즘을 application에 제공을 하는 두 가지의 목표가 있음 

- Operating System은 2가지 목표를 달성하기 위해서 아래와 같은 기본적인 추상화를 이룸
![one](/cheewr85/img/computerarchitecture/nine.png)

- I/O Devices를 추상화하는 것은 Files이고, I/O Devices와 main memory를 추상화하는 것은 Virtual Memory이며, processor, main memory, I/O Devices를 추상화한 것이 processes임

#### Processes
- 앞서 말한대로 hello program을 실행을 하면 Operating System이 마치 program이 시스템에서 오직 하나만 돌아가는 것처럼 보여줌

- 마치 프로그램이 processor, main memory, I/O devices를 독점적으로 사용하게끔 하는 것처럼 보이게 함

- 이렇게 착각을 불러일으키게 되는 것은 process라는 개념에 의해서 그렇게 보이는 것임

- process는 실행되는 프로그램에 대한 operating system의 추상화임

- 여러 개의 process들은 같은 시스템에서 동시에 실행될 수 있고 각각의 process는 하드웨어의 사용을 독점적으로 쓰는 것처럼 보임

- Concurrently 즉, 동시성은 하나의 process에서의 명령이 다른 process의 명령에 interleave하는 것을 의미함

- 대부분의 시스템에서는 CPU가 그들을 실행시키는 것보다 더 많은 process들이 실행되고 있음

- 예전에 전통적인 시스템은 오직 한 번에 하나의 프로그램을 실행시켰지만, 새로운 multi-core processors는 여러개의 프로그램을 동시에 실행시킬 수 있음

- 단일 CPU는 processor가 switch를 함으로써 여러개의 processes들이 동시에 실행되는 것처럼 보임

- Operating System이 이러한 interleaving한 기법을 Context Switching이라고 함(하지만 이해를 위해서 아래와 같은 경우는 uniprocessor system으로 가정함)

- Operating System은 process가 실행시키기 위해서 정보의 모든 상태를 추적을 함, 이 상태를 context라고 함

- context에서는 현재 PC(Program Counter)의 상태값, register file, main memory의 구성 상태를 포함함

- 어떠한 상태이든 uniprocessor system에서는 단일 process를 위해서 code를 실행함, operating system이 현재 process에서 새로운 process로 바꾸려고 결정했을 때, 현재 process의 context를 저장하고 새로운 process의 context를 가지고오는 context switch를 실행함, 그리고 새로운 process에 control을 넘겨줌, 아래와 같은 흐름을 가짐
![one](/cheewr85/img/computerarchitecture/ten.png)

- 이 과정을 풀어서 설명하면 shell process와 hello process가 존재함, 여기서 처음엔 shell process가 혼자서 실행이 되고 명령어 입력을 기다림, hello program 실행을 요청하면 shell은 우리의 요청을 수행받아 system call이라는 특별한 함수를 불러일으켜 operating system의 control을 넘김

- operating system은 shell context를 저장하고 hello process와 context를 생성함, 그리고 control을 hello process에 넘김, 그리고 hello가 끝났을 때 shell process의 context를 다시 불러와 control을 넘기고 다음 명령어를 기다림

- 이 과정이 위의 그림에 나온 것임, 여기서 process간의 변환은 operating system에서 kernel이 이 역할을 함, 이 kernel은 operating system code에 일부로 memory에 항상 상주해 있음

- application program의 action이 파일을 읽거나 쓰는 것 같이 operating system을 필요로 한다면 특별한 system call 명령어가 실행되어 kernel로 control을 넘김, 그러면 kernel은 요청된 실행을 수행하고 application program에 return을 함

- kernel은 분리된 process가 아님, 대신 code와 data 구조의 집합체로 시스템이 모든 processes를 관리하는데 사용함

- 이를 저수준의 hardware와 operating system software와도 접목시켜서 추상화를 볼 수 있음

#### Threads
- process가 단일한 control flow를 가지고 있다고 생각하지만 현대 시스템에서는 process는 threads라고 불리는 다양한 실행 유닛으로 구성되게 할 수 있음

- threads는 각각 process의 context를 실행시킬수 있고 같은 코드와 global data를 공유할 수 있음

- Threads는 network servers에서 동시성을 필요로 하기 때문에 중요한 프로그래밍 모델 중 하나임

- 왜냐하면 여러개의 processes 보다 여러개의 threads 사이의 데이터를 공유하는 것이 더 쉽기 때문임, 그리고 threads가 processes보다 더 효율적임

- multiple processor가 이용 가능할 때 Multi-threading은 프로그램을 더 빠르게 할 수 있는 방법 중에 하나임

#### Virtual Memory
- Virtual Memory는 각각의 process가 main memory를 독점적으로 사용하는 것처럼 보이게 함

- 각각의 process는 virtual address space라고 알려진 같은 표준의 memory를 가지고 있음

- Linux processes는 아래와 같이 virtual address space를 가짐(Unix도 유사함)
![one](/cheewr85/img/computerarchitecture/eleven.png)

- 최상단에는 모든 process가 공통인 operating system에서의 data와 code가 있음, 아래로 갈수록 user process에 의해 정의된 code와 data를 가지고 있음, addresses는 아래에서 위로 올라가는 구조임

- 이 virtual address space는 각각 process가 잘 정의된 구역의 집합체로 각각이 특정 목적을 가짐

##### Program code and data
- code와 data는 실행가능한 파일의 contents로부터 직접적으로 초기화가 됨

##### Heap
- 위의 code와 data는 바로 run-time heap으로 넘어감, 이 영역은 process가 시작되면 사이즈가 고정이 됨

- 이 영역은 run time 시점에서 다이나믹하게 늘리고 줄일 수 있는 영역임

##### Shared libraries
- 중간에서 code와 data를 shared libraries를 위해서 잡아둔 곳임, 이는 예를 들면 C의 표준 라이브러리 같은 것을 볼 수 있음

##### Stack
- compiler가 함수 호출을 실행하기 위해서 사용을 하는 곳임

- heap과 같이 프로그램이 실행되는 동안 다이나믹하게 늘리고 줄일 수 있는 영역임

- 특별하게 함수를 호출하는 순간마다 stack이 늘어나고 함수가 return 될 때마다 줄어듬

##### Kernel virtual memory
- Application program은 직접적으로 접근을 할 수 없고 kernel을 통해서만 이쪽에 대한 작업을 수행할 수 있음

#### 정리
- 이런식으로 Vietual memory가 작업을 위해서는 hardware와 operating system software, 그리고 processor가 생성한 모든 주소를 hardware가 변환하는 작업을 포함하여 복잡한 상호작용을 필요로 함

- 기본 아이디어는 disk의 process's virtual memory의 contents를 저장하고 그때 disk를 위해 cache로써 main memory를 사용하는 것임

#### Files
- File은 bytes의 sequence임, 모든 I/O device는 disk, keyboard, display, network마저도 file로 모델링 됨 

- 시스템의 모든 입출력은 Unix I/O라고 알려진 시스템콜의 작은 집합을 사용함으로써 file을 읽고 씀

- 이 개념은 applications에 시스템이 가지고 있는 다양한 모든 I/O devices의 표준화된 규격을 제공할 수 있음

- 이를 통해서 application programmer는 세부적인 사항을 알지 못하더라도 조작을 할 수 있게 되는 것임

------------

### 네트워크를 사용한 다른 System과의 Communicate
- 현대 시스템에서는 네트워크를 통해서 다른 시스템들과 연결되어 있음

- 단일 시스템의 관점에서 network 역시 I/O devices의 일부로 볼 수 있음(아래와 같이)
![one](/cheewr85/img/computerarchitecture/twelve.png)

- 시스템이 main memory로부터 bytes의 연속성을 복사해 network adapter로 보낼 수 있음, 이 data 흐름은 마치 local disk drive로 보내는 것과 유사함

- 그리고 시스템은 이렇게 보내진 data를 읽고 main memory로 복사할 수 있음

- Internet의 발달로 인해서 이런식으로 정보를 하나의 기기에서 다른 곳으로 복사하는 것이 중요해짐, email, instant messaging, World Wide Web, FTP, telnet등이 네트워크를 통한 위에서 설명한 흐름이 다 기반이 됨

- 이 과정이 위의 그림에서 telent을 통한 네트워크를 기반으로 통신을 하는 것으로 볼 수 있음 즉, Remote shell이 hello program을 실행시키는 것임, 앞서 봤던 Computer System 내에서 이루어진 것들이

- 이런식으로 clients와 servers가 일반적으로 주고 받는 방식이 모든 network applications의 기본임