## 액티비티 생명주기
![one](/cheewr85/img/two.png)

- 사용자가 앱을 탐색하고, 앱에서 나가고, 앱으로 다시 돌아가면 앱의 Activity 인스턴스는 수명 주기 안에서 서로 다른 상태를 통해 전환됨
- Activity 클래스는 활동이 상태 변화(시스템이 활동을 생성, 중단 또는 다시 시작하거나, 활동이 있는 프로세스를 종료하는 등)를 알아차릴 수 있는 여러 콜백을 제공함
- 이 생명주기를 통해서 각 콜백은 상태 변화에 적합한 특정 작업을 실행할 수 있도록 함, 적시에 알맞은 작업을 하고 적절하게 전환을 처리하여 앱이 더욱 안정적으로 기능하게 함
- 이 생명주기를 잘 활용하여서 다음과 같은 문제가 발생하지 않게 할 수 있음
	- 사용자가 앱을 사용하는 도중에 전화가 걸려오거나 다른 앱으로 전환할 때 비정상 종료되는 문제
	- 사용자가 앱을 활발하게 사용하지 않는 경우 귀중한 시스템 리소스가 소비되는 문제
	- 사용자가 앱에서 나갔다가 나중에 돌아왔을 때 사용자의 진행 상태가 저장되지 않는 문제
	- 화면이 가로 방향과 세로 방향 간에 회전할 경우, 비정상 종료되거나 사용자의 진행 상태가 저장되지 않는 문제
- 전체적인 패러다임은 위와 같은 그림에 의해서 진행이 됨, 이 패러다임을 이해하고 사용자가 예상한 대로 앱이 동작하도록 필요한 수명 주기 메소드를 구현하는게 중요함

- onCreate의 경우, 시스템이 먼저 활동을 생성할 때 실행되는 것으로, 필수적으로 구현해야 함, 기본 애플리케이션 시작 로직을 실행함, 데이터를 목록에 바인딩하거나 활동을 ViewModel과 연결하거나 일부 클래스 범위 변수를 인스턴스화 할 수도 있음
- 여기서 saveInstanceState 매개변수를 받는데 이는 활동의 이전 상태가 포함된 Bundle 객체임

- onStart의 경우, 활동이 시작됨 상태에 들어가면 호출됨, 활동이 사용자에게 표시되고, 앱은 활동을 포그라운드에 보내 상호작용할 수 있도록 준비함

- onResume의 경우, 활동이 재개됨 상태에 들어가면 포그라운드에 표시되고 호출됨, 앱이 사용자와 상호작용을 함, 어떤 이벤트가 발생하여 앱에서 포커스가 떠날 때까지 앱이 이 상태에 머무름
- 방해되는 이벤트 발생시 활동은 일시중지됨 상태에 들어가고 onPause를 호출함
- 일시중지에서 재개됨으로 돌아오면 시스테밍 onResume을 다시 한 번 호출함, 그래서 onPause와 관련된 구성요소를 초기화하고 재개됨에 따라 필요한 다른 초기화 작업도 필요함

- onPause의 경우, 사용자가 활동을 떠나는 것을 나타내는 첫 번째 신호로 호출됨, 항상 소멸되는 것은 아님
- 활동이 포그라운드에 있지 않게 되었다는 것을 나타냄, 다시 시작할 작업을 일시중지하거나 조정함
- 사용자가 필요로 하지 않을 때 배터리 수명에 영향을 미칠 수 있는 모든 리소스를 해제할 수 있음

- onStop의 경우, 활동이 사용자에게 더 이상 표시되지 않으면 중단됨 상태에 들어가서 해당 콜백이 호출됨
- 앱이 사용자에게 보이지 않는 동안 앱은 필요하지 않은 리소르를 해제하거나 조정해야함
- CPU를 비교적 많이 소모하는 종료 작업을 실행해야함

- onDestroy의 경우 활동이 소멸되기 전에 호출됨, 활동이 종료되는 경우(사용자가 활동을 아예 닫거나 finish()가 호출될 때), 구성 변경(기기 회전, 멀티 윈도우 모드)으로 인해 시스템이 일시적으로 활동을 소멸시키는 경우

- 아래의 표와 같이 프로세스 수명 주기와 Activity 상태 간의 관계가 있음
![one](/cheewr85/img/five.PNG)

- 추가적으로 Activity의 회전 또는 멀티 윈도우 모드로 전환 시, 시스템은 이런 구성 변경이 발생하면 Activity를 소멸시켜 Activity 인스턴스에 저장된 모든 UI 상태를 제거함
- 그래서 이런 부분에 대해서 ViewModel 또는 onSaveInstanceState 또는 로컬 저장소를 결합하여 사용자의 임시 UI 상태를 보존해야함
- 시스템이 이전 상태를 복원하기 위해 사용하는 저장된 데이터를 인스턴스 상태라고 하고 이는 Bundle 객체에 저장된 키-값 쌍의 컬렉션임
- Bundle 인스턴스 상태를 사용하여 Activity 레이아웃의 각 View 객체 관련 정보를 저장함
- 그래서 onSaveInstance를 통해서 해당 정보에 대해서 저장할 수 있음

- 여기서 Activity가 다른 화면으로 넘어가 새로운 화면에서 새로 Activity를 시작할 때 결과를 받는 것에 따라서 startActivity나 startActivityForResult 중 하나를 사용하여 시작함
- 두 가지 모두 Intent를 통해서 전달됨(이를 통해서 시작하고자 하는 Activity를 정확히 나타내고 실행하고자 하는 작업의 유형을 설명함)
- startActivity의 경우 결과를 반환할 필요가 없는 경우 쓰는 것이고 startActivityForResult의 경우 결과를 반환받고자 할 때 쓰는 것임
- 이런 과정속에서 두 Activity의 수명주기가 모두 전환됨 즉 Activity A에서 B로 넘어갈 때 A에서는 onPause를 그리고 B에서는 onCreate, onStart, onResume을 통해서 시작이 되고 A에 경우 더 이상 표시가 되지 않으므로 onStop이 시작됨
- 이런식으로 수명 주기 콜백의 순서를 예측할 수 있어 이런 전환이 일어날 때의 정보를 관리할 수 있음