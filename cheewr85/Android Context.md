# Android Context
- Context는 애플리케이션 환경에 대한 글로벌 정보를 갖는 인터페이스임

- Android 시스템에서 구현체를 제공하는 추상 클래스로, 애플리케이션 별 리소스 및 클래스 접근에 사용되고, Activity 실행, Broadcast, Intent 수신등과 같은 애플리케이션 수준 작업에 사용됨

- 정리하면 먼저 애플리케이션의 현재 상태를 나타냄

- Activity와 애플리케이션의 정보를 얻기 위해서 사용할 수 있음

- 리소스, 데이터베이스, SharedPreferences등에 접근하기 위해 사용할 수 있음

- Activity와 애플리케이션 클래스는 Context 클래스를 확장한 클래스임

- 세부적으로 아래와 같이 2가지로 나눌 수 있음

## Application Context
- Activity에서 `getApplicationContext()`를 통해 접근할 수 있음

- 애플리케이션 라이프사이클에 묶여있고, Context가 종료된 이후에도 Context가 필요한 작업이나 Activity Scope를 벗어난 Context가 필요한 작업에 적합함

- 애플리케이션 객체를 생성하고 Context가 필요할 떄 Application Context를 전달해야함, 이때 Activity Context를 전달하면 Activity가 화면에 표시되지 않는 순간에도 Garbage Collection이 진행되지 않아 메모리 누수가 발생함

![one](/cheewr85/img/eleven.png)

- 좀 더 확실한 예를 설명하면 앱에서 데이터베이스를 관장하는 AppDatabase라는 클래스가 싱글톤으로 존재한다고 할 때, 이를 초기화시 Context가 필요한데 이때 Application Context가 필요함

- AppDatabase가 싱글톤인데 계속 Activity Context를 참조하고 있으면 메모리 누수가 일어남(이런 과정 자체가 배치되는 상황임, 싱글톤 자체를 그런 메모리 낭비를 막기위한 것인데, 그리고 전체 프로젝트에서 지속적으로 사용해야 하는 것이므로)

- Activity는 그리고 Garbage Collection이 되지 않기 때문에 이런 Activity Context를 참고하면 안됨

- 그리고 GUI와 관련된 Context는 사용하면 안됨

## Activity Context
- Activity 내에서 유효한 Context임, Activity 수명주기와 연결되어 있음, Activity와 함께 소멸해야 하는 경우 사용함

- Activity와 수명주기가 같은 객체를 생성해야 할 때 사용함

- 위의 예시에서 Application Context는 MyApplication, MainActivity, SubActivity 모두 사용할 수 있지만 Activity Context는 각각 Activity에서만 사용할 수 있음

- 그렇담 위에서 봤듯이 Application Context는 싱글톤 패턴과 DB의 액세스 이런 과정에서 쓰지만 이런 Activity Context는 Toast, Dialog 등 UI 기능에 대해서 Context가 필요할  사용함