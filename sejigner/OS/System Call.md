# System Call
사용자 프로세스가 소프트웨어 인터럽트를 발생시켜 커널에서 특권 명령을 실행하고자 할 때 운영체제에 요청하는 것을 의미

## 처리 과정
1. 사용자 프로세스가 시스템 콜을 요청하면 유저 모드에서 커널 모드로 전환
2. 시스템 콜은 기능 별로 고유번호를 할당 받고, 해당하는 제어 루틴이 커널 내부에 미리 정의됨
3. 커널이 시스템 콜의 고유 번호를 확인하고 대응하는 기능 번호에 따라 서비스 루틴 호출
4. 서비스 루틴 실행 후 커널 모드에서 유저 모드로 전환

## 시스템 콜의 기능
1. 유저 모드의 응용 프로그램이 커널의 기능을 사용하도록 함
2. 시스템 호출 시 사용자 모드에서 커널 모드로 전환
3. 시스템 호출이 처리되면 커널 모드에서 사용자 모드로 전환

## 시스템 콜의 유형
### 프로세스 제어(Process Control)

-   끝내기(end), 중지(abort)
-   적재(load), 실행(execute)
-   프로세스 생성(create process)
-   프로세스 속성 획득과 설정(get process attribute and set process attribute)
-   시간 대기(wait time)
-   사건 대기(wait event)
-   사건을 알림(signal event)
-   메모리 할당 및 해제 : malloc, free

### 파일 조작(File Manipulation)

-   파일 생성(create file), 파일 삭제(delete file)
-   열기(open), 닫기(close)
-   읽기(read), 쓰기(write), 위치 변경(reposition)
-   파일 속성 획득 및 설정(get file attribute and set file attribute)

### 장치 관리(Devide Management)

-   장치를 요구(request devices), 장치를 방출release device)
-   읽기, 쓰기, 위치 변경
-   장치 속성 획득, 장치 속성 설정
-   장치의 논리적 부착(attach) 또는 분리(detach)

### 정보 유지(Information Maintenance)

-   시간과 날짜의 설정과 획득(time)
-   시스템 데이터의 설정과 획득(date)
-   프로세스 파일, 장치 속성의 획득 및 설정

### 통신(Communication)

-   통신 연결의 생성, 제거
-   메시지의 송신, 수신
-   상태 정보 전달
-   원격 장치의 부착 및 분리

## 시스템 콜의 매개변수 전송
기능 또는 시스템 환경에 따라 시스템 콜이 발생할 때 더 많은 정보가 필요할 수 있다. 이때 세 가지 방법이 가능
1. 레지스터를 통한 전송
	- 매개 변수를 cpu 레지스터에 전송한다. 매개변수가 레지스터의 갯수보다 많을 수 있으므로 좋지 않음
	- 레지스터는 한정된 자원
2. 블록 or 테이블을 통한 전송
	- 메모리 내 블록이나 테이블에 저장 후 메모리의 주소가 레지스터에 저장됨
3. 스택을 통한 전송
	- 매개변수는 프로그램에 의해 스택으로 전달될 수 있음
	- 프로그램이 스택에 올리고, 운영체제가 꺼냄

> Written with [StackEdit](https://stackedit.io/).
