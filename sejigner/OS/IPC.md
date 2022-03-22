# IPC (Inter-Process Communication)
## IPC의 정의
![ipc](/sejigner/img/os/ipc/1.PNG)
- 프로세스 사이에서 데이터를 주고 받는 행위
- 프로세스는 서로 독립된 상태로, 통신을 위해서는 **커널**의 IPC를 거쳐야 함 

## IPC 종류
상황에 따라 효율적인 장비가 있음.

![ipc](/sejigner/img/os/ipc/2.PNG)

### 익명 PIPE
![ipc](/sejigner/img/os/ipc/3.PNG)
- 하나의 프로세스는 write, 다른 하나의 프로세스는 read
- 부모 자식 간 단방향 통신으로 자주 사용
- Half-duplex(반이중) 통신이라고도 불림
- read(), write()는 block mode로 작동되므로 순차적으로 진행됨
- 장점
	- 간단한 사용
- 단점
	- Full-duplex(전이중) 통신을 위해서는 PIPE 두 개가 필요한데, 구현이 복잡해질 수 있음
	- buffer가 작아 overflow 발생 확률 높음 (빠른 read 처리 필요)
	- 부모 자식 간 프로세스 사이에서 가능

### Named PIPE(FIFO)
![ipc](/sejigner/img/os/ipc/4.PNG)
- 이름을 가진 PIPE를 통해 프로세스 간 단방향 통신 지원
- 서로 다른 프로세스들이 통신
- 장점 
	- 이름이 있어서 부모 자식 관계가 아닌 프로세스끼리도 통신이 가능함
- 단점
	- read, write 동시 실행 불가
	- 전이중 통신을 위해서는 FIFO파일 두 개 필요

### Message Queue
![ipc](/sejigner/img/os/ipc/5.png)
- named pipe와 같은 선입선출 방식
- Named Pipe가 데이터의 흐름이라면 Message Queue는 메모리 공간
- 장점
	- 데이터에 주소가 붙어있어 다수의 프로세스가 동시에 접근 가능
	- 비동기 방식으로 방대한 양 처리 가능
- 단점
	- 메시지가 실제로 전달이 됐는지 확인 불가
	- 큐에 데이터를 넣고 빼는 과정에서 오버헤드 발생 가능
	- 데이터가 많아지면 추가적인 메모리 자원 필요

### Shared Memory
![ipc](/sejigner/img/os/ipc/6.png)
- 위에서 다룬 방식은 전부 통신이지만 공유 메모리는 통신이 아닌, 공유를 통한 방식
- 프로세스가 메모리 영역을 공유하도록 하여 함께 사용하도록 함
- 프로세스가 공유 메모리를 할당하려면 커널에 요청해야하고, 커널이 할당하면 다른 프로세스에서 해당 메모리 영역에 접근이 가능해짐
- 장점
	- 공유 메모리는 중개자 없이 곧바로 메모리에 접근이 가능하므로 모든 IPC 중 가장 빠름
- 단점
	- 메시지 전달 방식이 아니라 데이터 접근 시점을 알 수 없음
	- 커널 설정에 종속적이므로 커널의 공유 메모리 허용치를 확인해야 함

### Memory Map
- 메모리를 공유한다는 점에서 공유 메모리와 유사
- 공유 메모리와는 달리 메모리 맵은 열린 파일을 메모리에 맵핑시켜 공유
- 파일을 활용하면 운영체제의 전역적인 자원을 사용하는 것이므로 프로세스 간 공유가 문제 없음
- 장점
	- 데이터가 메모리에 올라와있는 것처럼 간단히 접근 가능
	- 일반적인 파일 IO에 비해 나은 성능
	- 비동기 IO 방식을 시스템에서 처리해주기 때문에 스레드 문제 X
- 단점
	- 일반 파일 IO에 비해 상당히 많은 메모리 요구
	- 메모리 사용을 얼마나 오래 지속할 것인가 컨트롤 불가능

### Socket
- 같은 도메인 내에서 프로세스 간 통신 지원
- 서버단에서 bind, listen, accept로 소켓 연결 준비
- 클라이언트단에서 connect를 통해 서버에 요청
- 연결이 되면 Socket을 send하여 데이터 통신 시작
- 통신이 끝나면 Socket을 close()
- 네트워크에서 자세히 다룸

### Semaphore
- 다른 IPC 설비가 프로세스간 메시지 전송을 목적으로 하는데 반해, Semaphore는 프로세스 간 데이터를 동기화하고 보호하는 역할
- 하나의 공유 자원에 여러 프로세스가 동시에 접근하는 것이 문제가 될 경우에 공유자원 또는 임계영역에 접근이 가능한 다른 프로세스의 갯수를 제한
- 임계영역 : 각 프로세스엣어 공유데이터를 접근하는 프로그램 코드 부분
- Mutex(Mutual Exclusion) : 세마포어와 같은 목적이지만, 하나의 프로세스 혹은 스레드가 접근하는 것을 막아준다는 점에서, 동기화 대상이 하나 이상인 세마포어와 상이 (뮤텍스가 세마포어에 포함되는 개념)

## Reference
https://jwprogramming.tistory.com/54

https://doitnow-man.tistory.com/110

https://www.byfuls.com/programming/read?id=63

> Written with [StackEdit](https://stackedit.io/).
