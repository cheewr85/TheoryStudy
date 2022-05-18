# Process
- 기본적으로 같은 애플리케이션의 모든 구성 요소는 같은 Process와 Thread에서 실행됨

- 애플리케이션 내의 여러 가지 구성 요소가 각자 별도의 Process에서 실행되도록 할 수도 있고, 어느 Process에나 추가 Thread를 만들 수 있음

- 여기서 말하는 각 유형의 구성 요소는 Activity, Service, BroadcastReceiver등의 구성 요소를 의미함

- 즉, 여기서 각 구성 요소를 자체 Process에서 실행하거나 다른 구성 요소를 제외한 일부 구성 요소만 Process를 공유하게 할 수 있음

- 메모리가 부족하거나 사용자에게 더욱 즉각적인 서비스를 제공하는 다른 Process가 이 Process를 중단해야 하는 경우 등이 있음, 이러면 중단된 Process에서 실행되고 있던 애플리케이션 구성 요소도 따라서 소멸됨

- 그와 같은 구성 요소에 수행할 작업이 다시 생기면 그에 대한 Process도 다시 시작됨

- 이러한 Process 종료를 결정하는 것은 해당 Process에서 실행되는 구성 요소의 상태에 따라 달라짐

- 이런 Process의 세부적인 구성과 내용은 아래의 공식문서를 통해서 더 자세히 알 수 있음
- https://developer.android.com/guide/components/activities/process-lifecycle

## UI Thread
- 애플리케이션이 시작되면 시스템이 애플리케이션에 대한 실행의 Thread를 생성하며 이를 기본이라고 함

- 이 Thread는 드로어블 이벤트를 포함하여 적저러한 사용자 인터페이스 위젯에 이벤트를 발송하는 역할을 함

- 대부분의 경우 이것은 Android UI 도구 키트의 구성 요소와 개발자의 애플리케이션이 상호작용하는 Thread임, 기본 Thread는 UI Thread라고 불릴 수 있음

- 하지만 특수한 상황에서 이 기본 Thread가 UI Thread가 아닐 수 있음

- 시스템의 구성 요소의 각 인스턴스에 대해 별도의 Thread를 생성하지 않음, 같은 Process에서 실행되는 모든 구성 요소는 UI Thread에서 인스턴스화 되고 각 구성 요소에 대한 시스템 호출은 해당 Thread에서 발송됨

- 따라서 시스템 콜백에 응답하는 메소드(사용자 작업을 보고하는 onKeyDown 혹은 수명 주기 콜백 메소드)는 항상 Process의 UI Thread에서 실행됨

- 여기서 주의할 점은 모든 것이 UI Thread에서 발생할 경우 네트워크 액세스나 데이터베이스 쿼리 등의 긴 작업을 수행할 때 전체 UI가 차단됨, Thread가 차단되면 모든 이벤트가 발송되지 않음, 마치 사용자에게 애플리케이션이 중단된 것처럼 보임

- 더욱 심각한 것은 UI Thread가 몇 초 이상 차단되면(현재 약 5초) 사용자에게 ANR(애플리케이션이 응답하지 않습니다.)라는 대화상자가 표시됨, 이러면 사용자가 애플리케이션을 종료할 수도 있고, 삭제할 수도 있음

- 추가적으로 두 가지를 알아야함 인터페이스 조작 작업은 모두 UI Thread에서만 해야함

- UI Thread를 차단하지 말 것, UI Thread 외부에서 Android UI 도구 키트에 액세스하지 말 것

## Worker Thread
- 애플리케이션 UI가 반응하기 위해서는 UI Thread를 차단하지 않는 것이 매우 중요함

- 수행은 해야 하지만 즉각적인 조치가 필요하지 않은 작업일 경우, 반드시 별도의 Thread에서 수행해야함(Background 또는 Worker Thread)

- UI Thread나 기본 Thread를 제외한 다른 Thread에서 UI를 업데이트할 수 없음

- 이 문제를 처리하기 위해서 다른 Thread에서 UI Thread로 액세스 하는 여러가지 방법을 제공함

- Activity.runOnUiThread(Runnable) 등
```kotlin
// 네트워크 작업은 별도의 Thread에서 수행되는 반면 ImageView는 언제나 UI Thread에서 조작되기 때문
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

- 그러나 작업이 복잡해질수록 이런 종류의 코드가 더 복잡해질 수 있고 유지관리하기 까다로워질 수 있음

- 더 복잡한 상호작용을 Worker Thread로 처리하려면, Worker Thread에서 Handler를 사용하여 UI Thread에서 전달받은 메시지를 처리하는 방안을 고려할 수 있음

- 비동기 처리를 하여서 이를 효과적으로 처리할 수 있음

### 정리
- 위의 Process와 Thread를 도식화 시키면 아래와 같음
![one](/cheewr85/img/android/twelve.png)

- 여기서 가장 중요한 것은 UI Thread에서는 긴 시간이 걸리는 작업을 지양해야하는 것 그리고 Worker Thread에서는 UI 작업을 하지 않는 것임

- 이는 자연스럽게 네트워크, 복잡한 계산, DB 쿼리 작업을 Worker Thread에서 처리하도록 권고를 함 위에서 UI Thread에서는 과도한 작업을 하면 ANR 에러가 나오는 것임, 이를 고려해야함

- 그래서 위의 구조처럼 자연스럽게 멀티 Thread를 사용하게 됨, UI Thread에서 DB 쿼리 작업, 네트워크 등의 작업을 하지 않기 때문에

## Message Queue
- Message 형태를 큐 형태로 관리하는 자료구조임, 큐는 말 그대로 우리가 알고 있는 자료구조임, Message는 큐에 들어온 순서에 따라 차례대로 저장됨, 그리고 가장 먼저 들어온 Message 객체부터 순서대로 처리됨

- 이 Message Queue의 경우 앱의 메인 Thread에서 기본적으로 사용되고 있음, 이 Message 전달은 Message Queue에 연결된 Handler를 통해서 그리고 Message Queue로부터 Message를 꺼내고 처리하는 역할은 Looper가 수행을 함

### Message
- Thread 통신에서 Handler를 사용해서 데이터를 보내기 위해 데이터 종류를 식별할 수 있는 식별자와 실질적인 데이터를 저장한 객체, 그리고 추가 정보를 전달할 객체가 필요함, 이런 전달할 데이터를 한 곳에 저장하는 역할을 하는 클래스가 필요한데 이 역할을 Message 클래스가 함

- 하나의 데이터를 보내기 위해 한 개의 Message 인스턴스가 필요하고 일단 데이터를 담은 Message 객체를 Handler로 보내면 해당 객체는 Handler와 연결된 Message Queue에 쌓이게 됨

#### 도식화
![one](/cheewr85/img/android/thirteen.png)

## Handler
- 위에 도식화와 Message Queue에서 설명했지만 Handler는 Message Queue에서 던져주는 Message 혹은 Runnable을 처리하는 역할을 함

- 이 Handler는 Handler를 생성한 해당 Thread의 객체에 종속됨

- 이 Handler에서 sendMessage를 통해서 Message 객체를 보낼 수 있고 handlerMessage를 통해서 보낸 Message를 받아 처리할 수 있음

- 이를 통해서 받은 Message에 따라서 UI 변경등을 요청할 수 있음

### Looper
- 여기서 메시지 처리를 위해서 Message Queue로부터 Message를 꺼내온 다음, 해당 Message와 연결된 Handler를 호출하는 역할은 Looper가 담당함

- 메시지 처리를 위한 메시지 루프를 실행하는 것, 메인 Thread에는 기본적으로 생성되어 있지만 새로 생성한 다른 Thread에서는 Message나 Runnable을 받기 위해 Looper를 생성해줘야함

### Runnable
- 해당 Handler Thread가 실행할 run메소드와 그 내부에서 실행될 코드를 담음