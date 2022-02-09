# Asynchronous

## Asynchronous vs Synchronous

- Synchronous 동기
  
  - 요청을 보냈을 때 응답이 돌아와야 다음 동작 수행
  
  - 요청과 응답이 동시에 일어남
  
  - 장점 : 설계가 간단하고 직관적
  
  - 단점 : 결과가 주어질 때까지 로직 내에서 대기

- Asynchronous 비동기
  
  - 요청과 응답이 동시에 발생하지 않음
  
  - 응답을 받을 때 콜백 함수가 실행되어 결과를 반환
  
  - 장점 : 여러 작업을 동시에 수행하여 자원의 효율적인 관리가 가능
  
  - 단점 :  동기 작업 설계보다 복잡

![](https://t1.daumcdn.net/cfile/tistory/995FCE405C5EC51A30)

![동기 vs 비동기. (Synchronous vs Asynchronous)](https://t1.daumcdn.net/cfile/tistory/998AA03D5F26D54F0C)

동기 실행에서 한 작업이 몇 초, 몇 십초 이상 소요되면 그 동안 메인 쓰레드가 막혀서 ANR이 발생할 수 있음 -> 별도의 스레드에서 작업을 하게 하여 메인 스레드 실행에 영향이 가지 않도록 함

### 비동기 실행의 패턴

- 실행 : 비동기 작업 준비 및 시작

- 백그라운드 작업 : 백그라운드 스레드에서 비동기 작업

- 진행 사항 업데이트 : 백그라운드 스레드의 진행 상황을 메인스레드로 전달

- 비동기 실행 완료 후 처리 : 백그라운드 스레드 완료 후 메인 스레드에 완료 상태 전달

### AsyncTask

- 별도 스레드에서 작업하고 작업이 끝나면 메인 스레드에 Message를 전달하여 메인 스레드에서 그에 따른 처리를 하도록 함

- 핸들러를 사용하지 않고도 백그라운드 작업을 하면서 UI를 업데이트할 수 있게 하는 API

- 추상 클래스로서 AsyncTask를 상속한 클래스를 생성하여 사용

- 크기가 큰 파일 복사, 데이터베이스 쿼리, HTTP 서버 API 요청, FTP 파일 다운로드 등

- UI로의 통합을 목적으로 사용되면서 컨텍스트 leak이 발생하거나 콜백이 누락되거나 UI 업데이트 과정 중 충돌 등 안정성이 떨어져 **API level 30부터 deprecated** -> Corroutine 사용 권장

- ![](https://t1.daumcdn.net/cfile/tistory/2420B240577D4A720F)

---

### RxJava

반응형 프로그래밍을 위하여 개발 됨

> 반응형 프로그래밍 (Reactive Programming)
> 
> 주변 환경과 계속 상호 작용하는 프로그래밍. 환경이 변화하면 이벤트를 받아서 처리한다. 시간 순으로 들어오는 모든 데이터를 stream으로 처리한다.

RxJava = Observable + Observers + Schedulers

- Observable : 데이터 스트림. 하나의 스레드에서 다른 스레드로 전달할 데이터 압축
  특정 주기 또는 생애 주기 중 일회적으로 데이터 방출
  데이터를 처리하고 다른 구성요소에 전달

- Observers : Observable에 의해 방출된 데이터 스트림을 소비
  Observable을 구독하고 Observable이 방출하는 데이터 수신

- Schedulers : Observable과 Observers가 실행되어야 할 스레드를 알려줌

-----

### Coroutine

비동기적으로 실행되는 코드를 간소화하기 위해 Android에서 사용할 수 있는 동시 실행 설계 패턴. 

- 기능적 특징
  
  - 경량 : 코루틴을 실행 중인 스레드를 차단하지 않는 suspend function을 지원하여 단일 스레드에서 여러개의 코루틴 실행 가능
    -> 동시에 여러가지 작업을 지원하면서 스레드를 차단하는 방식보다 메모리 절약
  
  - Memory leak 감소 : **구조화된 동시 실행(structured concurrency**을 사용하여 범위 내에서 작업 실행 - 새로운 코루틴은 오직 특정한 CoroutineScope 내에서만 이루어질 수 있음 -> 코루틴의 생애 주기의 한계를 없앰
  
  - 취소 지원
    
    ```kotlin
    val job = launch {
        repeat(1000) { i ->
            println("job: I'm sleeping $i ...")
            delay(500L)
        }
    }
    delay(1300L) // delay a bit
    println("main: I'm tired of waiting!")
    job.cancel() // cancels the job
    job.join() // waits for job's completion 
    println("main: Now I can quit.")
    ```
    
    ```
    job: I'm sleeping 0 ...
    job: I'm sleeping 1 ...
    job: I'm sleeping 2 ...
    main: I'm tired of waiting!
    main: Now I can quit.
    ```
  
  - Jetpack 통합 : Jetpack 라이브러리에 코루틴을 완전 지원하는 extension 포함(ViewModel 등)

```kotlin
private fun preventSend() {
        et_message_chat_log.isEnabled = false
        iv_send_chat_log.isEnabled = false
    } 

 private fun checkChatOver() {
        CoroutineScope(IO).launch {
            if (viewModel.isOver(UID, partnerUid!!).await()) {
                isOver = true
                withContext(Main) {
                    preventSend()
                }
            }
        }
    }
```

- I/O 작업용으로 예약된 스레드에서 비동기적 실행

- UI를 취급하기 위해 `withContext()` 함수를 사용하여 코루틴 실행을 Main 스레드로 이동

#### Flow

- 순차적으로 값을 내보내고 정상 또는 예외로 완료되는 비동기적 데이터 스트림

- 코루틴 상에서 리액티브 프로그래밍을 지원하기 위한 구성 요소

- 리액티브 프로그래밍에서는 하나의 데이터를 발행하는 발행자가 데이터의 소비자에게 지속적으로 데이터를 전달 -> **데이터 스트림**

- suspend function이 비동기적으로 하나의 값을 반환한다면 Flow는 비동기적으로 여러개의 값을 반환받음

----

- 과거 RxJava와 비교하였을 때 Coroutine이 그다지 매력적이지 않았으나, Flows, StateFlows 등의 등장으로 코루틴이 RxJava의 거의 모든 메서드를 커버하게 됨

- 현업에서는 비동기 프로그래밍을 위해 점차 RxJava에서 코루틴으로 옮겨가는 추세인 듯
  
  1. RxJava를 효과적으로 사용하기 위해서는 **반응형** 방식의 프로그래밍이 필요함
     코루틴 Flows를 통해 반응형 프로그래밍이 더욱 용이해짐
  
  2. 코루틴의 디버깅이 더 쉬움. 예외와 충돌의 원인이 어디인지 찾기 더 쉬움
  
  3. 문법이 더 쉽고 안드로이드 수명주기에 직관적인 통합이 가능
     RxJava보다 입문 난이도 낮음
  
  4. 안드로이드와 구글이 코루틴을 밀고 있음
     모루틴의 사용자 기반이 더 커져서 RxJava가 밀릴 수 밖에 없음
