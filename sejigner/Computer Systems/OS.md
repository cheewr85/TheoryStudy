# Operating System
- 운영체제 : 컴퓨터의 하드웨어와 소프트웨어 자원을 관리하고 컴퓨터 프로그램의 일반적인 서비스를 제공하는 시스템 소프트웨어
	- 사용자와 컴퓨터 하드웨어 사이에서 인터페이스 역할
	- 파일 관리, 메모리 관리, 프로세스 관리, 입출력 처리, 주변장치 관리 등 기본 기능을 수행하는 소프트웨어

## 메모리 관리
- 메인메모리 관리 : 메인메모리는 빠른 저장공간을 제공하는데 이는 CPU가 직접적으로 접근할 수 있다. 프로그램이 실행되기 위해서는 메인메모리에 올려져야한다.
	- 메인메모리의 어떤 부분이 무엇에 의해 사용되고 있고, 무엇이 사용되고 있지 않은지 추적
	- 멀티프로그래밍 중 어떤 프로세스가 언제, 얼만큼 메모리를 차지할지 결정
	- 프로세스의 요청 시 메모리 할당
	- 프로세스의 메모리 사용 후 할당 해제
- 장치 관리 : 운영체제는 장치에 맞는 드라이버를 통해 장치 통신을 관리한다.
	- I/O 컨트롤러를 통해 모든 장치 추적
	- 어떤 프로세스가 언제, 얼마나 오래 장치를 차지할지 결정
	- 효율적인 방식으로 장치 할당
	- 장치 할당 해제
- 파일 관리 : 쉬운 탐색과 사용을 위해 디렉토리의 방식으로 정리한다.
	- 정보와 위치, 상태 등을 추적
	- 리소스 할당 및 해제

## 그 밖의 기능
- 보안 : 비밀번호와 같은 기술을 활용하여 프로그램과 데이터로의 권한 없는 접근을 방지한다.
- 시스템 성능 제어 : 서비스를 위한 요청과 시스템 응답 간의 딜레이를 기록한다.
- 작업 회계 : 다양한 작업과 사용자에 의해 사용된 리소스와 시간을 감시하고 계산한다.
- 오류 감지 지원 : 덤프, 트레이스, 에러 메시지를 생성하고 디버깅과 오류 감지를 지원한다.
	- 덤프:코어 덤프의 준말로, 디버깅을 위해 프로그램의 상태를 프린터나 외장 하드 등에 복사하는 것
	- 트레이스:프로그램의 실행에 관한 정보를 기록하기 위한 로깅의 일종
- 다른 소프트웨어와 사용자 간 조정 : 컴파일러, 인터프리터, 어셈블러, 타 소프트웨어를 조정하여, 다양한 컴퓨터 시스템의 사용자에게 배치한다. 

> Written with [StackEdit](https://stackedit.io/).
