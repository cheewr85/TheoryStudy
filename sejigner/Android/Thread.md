# Thread

프로세스 내에서 순차적으로 실행되는 실행 흐름의 최소 단위

# UI Thread

- Main Thread라고도 불림

- 앱이 실행되면 시스템이 앱 실행을 위해 생성

- 드로잉 이벤트와 같이 이벤트를 UI에 반영

- 안드로이드 UI 툴킷(android.widget & android.view) 컴포넌트와 앱이 이 곳에서 상호작용

- 앱의 Main Thread가 UI Thread가 아닌 특수한 경우도 존재

- 시스템은 같은 프로세스 내 각각의 컴포넌트를 위해 분리된 쓰레드를 생성하지 않고 UI 쓰레드에서 객체화 됨

- 사용자의 행동 콜백(onKeyDown())이나 생명 주기 콜백과 같은 시스템 콜백 등은 프로세스의 UI 쓰레드에서 작동

- 사용자와 상호작용 중 과도한 작업을 UI 쓰레드에서 실행할 경우, 성능 저하로 이어질 수 있음

- 네트워크 접근이나 데이터베이스 쿼리 같이 시간이 오래 걸릴 수 있는 작업이 UI 쓰레드에서 이뤄질 경우 전체 UI가 멈출 수 있음

- 쓰레드가 멈추면, 화면을 그려주는 drawing event를 포함한 모든 이벤트가 전달되지 못함 -> '앱이 **다운**'

- 5초 이상 UI 쓰레드가 막히면 "application not responding"(ANR) 대화창이 나옴 -> UX에 큰 악영향

- UI와 관련된 모든 작업은 UI 쓰레드에서 이뤄져야 함

- message queue 수신을 대기하는 루프를 실행
  
  ----

# Worker Thread

- UI 쓰레드 차단으로 이어질 수 있는 UI와 관련없는 과도한 작업은 별도의 쓰레드에서 처리 ("background thread", "worker thread")

- UI 접근 불가 (Activity.runOnUiThread(Runnable), View.post(Runnable), View.postDelayed(Runnable, long) 등의 메서드로 UI 조작 가능)
  
  ```kotlin
  fun onClick(v: View) {
      Thread(Runnable {
          // a potentially time consuming task
          val bitmap = processBitMap("image.png")
          imageView.post {
              imageView.setImageBitmap(bitmap)
          }
      }).start()
  }
  ```
  
  ----
  
  # Thread communication
  
  쓰레드 간 데이터 전달이 필요
  
  ![](C:\Users\sejig\AppData\Roaming\marktext\images\2022-01-19-09-03-32-image.png)
  
  ![](C:\Users\sejig\AppData\Roaming\marktext\images\2022-01-19-09-04-37-image.png)
  
  - Message : 쓰레드 통신에서 핸들러를 통해 전달되는 데이터가 저장되는 클래스
    하나의 데이터 당 한 개의 Message 객체가 필요
    보내진 Message는 핸들러에 연결된 Message Queue에 쌓임
  
  - Message Queue : 쓰레드에서 받은 Message 객체를 큐 자료구조 형태로 관리
    FIFO 방식으로 동작
    MessageQueue 객체를 직접 참조하여 메시지를 전달하거나 처리하지 않고, Looper를 통해 메시지를 꺼내고, 다시 Handler를 통해서 전달
  
  - Looper : message queue에서 메시지를 꺼내서 메시지와 관련된 Handler를 호출
    즉, 메시지 처리를 위한 message loop 실행
  
  - Handler
    
    - UI 쓰레드에서 Message와 쓰레드의 MessageQueue와 연계된 Runnable를 받아서 처리
    
    - 각각의 Handler 객체는 단일 쓰레드와 그 쓰레드의 message queue와 연계됨
    
    - message와 runnable을 스케줄함
    
    - 생성과 동시에 현재 스레드의 루퍼와 메시지 큐에 대한 참조를 받음
    
    - 메시지를 보낼 때 이 참조 값을 사용하여 메시지 큐에 메시지 전달
    
    - 핸들러를 통해 다른 스레드가 아닌 핸들러가 속한 스레드로 메시지를 전달할 수 있음
    
    - 스레드 당 여러 개의 메시지 생성 가능 -> 메시지 종류와 기능에 따라 분류 권장

- Runnable 
  
  - Thread의 인터페이스 형태로, 구현할 메서드가 run() 하나 뿐
  
  - 자바에서 다중 상속이 허용되지 않아서 Thread 클래스를 확장하는 클래스가 다른 클래스를 상속받을 수 없을 경우, Thread 클래스를 상속받는 대신 Runnable 인터페이스를 통해 Thread 구현
  
  - 현업에서는 Thread 클래스 확장보다는 Runnable 클래스 구현이 선호됨
