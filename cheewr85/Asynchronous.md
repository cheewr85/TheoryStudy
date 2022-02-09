# 비동기 처리
- 앞서 Thread에서 살펴봤듯이 네트워크를 사용하여 정보를 받아 이를 업데이트 하는 작업이 있다고 할 때, 이를 Main Thread에서 수행을 하면 앱이 종료가 됨

- 이러한 이슈로 인해서 Main Thread에서 네트워크, 데이터베이스 쿼리 등의 작업을 하지 못하므로 이런 작업을 비동기 처리를 해주어야함

- 이러한 비동기 처리에 대해서 AsyncTask, RxJava, Coroutine등을 통해서 비동기 작업을 처리할 수 있음

## AsyncTask
- 가장 일반적으로 접근해서 처리할 수 있는 방법

- 비동기 프로세스는 execute -> doInBackground -> onProgressUpdate -> onPostExecute의 과정으로 진행된다고 볼 수 있음

- execute에서는 비동기 작업 준비 및 시작을 함, doInBackground에선 백그라운드 Thread에서 비동기 작업을 실행함

- onProgressUpdate에서는 백그라운드 Thread 진행 상황을 MainThread로 전달하고 onPostExecute에서는 백그라운드 Thread 완료 후 메인 Thread에 완료 상태 전달을 함

- 이 과정에서 AsyncTask를 응용할 수 있음, 메인 Thread와 별개로 비동기 실행이 필요한 작업에 사용할 수 있음

- 추상클래스로 선언이 되고 AsyncTask가 제네릭 클래스로 선언되어 사용됨 `AsyncTask<Params, Progress, Result>`, 여기서 각 메서드에서 사용할 데이터 타입을 결정할 수 있는 것

- Params에서는 AsyncTask 실행에 필요한 파라미터, Progress는 현재 작업 진행 정보를 나타내는 상태값, Result는 작업의 실행이 완료된 후의 최종결과를 나타냄

- 그러면 코드 상으로 아래와 같이 제네릭 클래스로 선언되고 메서드 타입이 결정은 아래와 같이 되는 것임
```java
class SomethingAsync extends AsyncTask<Void , Integer , Integer> {
        @Override
        protected void onPreExecute() {
            progressBar.setVisibility(View.VISIBLE);
        }
        @Override
        protected Integer doInBackground(Void... voids) { // Void 타입으로 결정됨, 아래도 동일
           

            return value;
        }
        @Override
        protected void onProgressUpdate(Integer ... values) {
            progressbar.setProgress(values[0].intValue());
        }
        
        @Override
        protected void onPostExecute(Integer result) {
         
        }
        
      
    }
```

- doInBackground는 Void, onProgressUpdate는 Integer, onPostExecute는 Integer 타입으로 변수 종류가 결정됨

- 여기서 doInBackground의 경우 AsyncTask를 execute할 때 전해줄 값을, onProgressUpdate는 진행상황을 업데이트할 때 사용할 값을 onPostExecute의 경우 AsyncTask가 끝난 뒤 결과값을 나타냄

- 이런식으로 AsyncTask를 정의하고 사용할 클래스에서 execute를 하여서 AsyncTask의 작업들이 비동기로 처리되어서 실행될 수 있음

## RxJava
- 비동기 처리에 있어서 비동기 데이터 흐름을 중시하는 프로그래밍으로 외부에서 자유롭게 데이터 입출력을 하여도 메인 쓰레드와 방해하지 않는 것을 중시하는 방법임

- 이는 반응형 프로그래밍으로써, 데이터의 변화가 발생한 곳에서 새로운 데이터를 Consumer에게 전달하는 방식임

- 이를 데이터 소스를 정의하고 그것의 변경사항을 받아서 프로그램에 알려줄 것을 JVM 위의 자바 언어로 구현해 놓은 라이브러리가 RxJava임

- 다수의 비동기 실행 흐름(Thread)을 생성하고, 그 결과를 취합하여 최종 리턴하도록 되고 비동기 흐름을 compose 해주는 연산자를 제공하며 콜백을 사용하지 않게 설계할 수 있음

- 여기서 RxJava를 활용하여 연산자(Operator)를 통해 기존 데이터를 참조, 변형하여 Observable을 생성하고 비동기 연산을 필터링, 변환, 조합할 수 있음

- 여기서 이 Observable은 데이터의 변화가 발생하는 데이터 소스로서, 데이터 흐름에 맞게 Consumer에게 알림을 보내는 Class임, 이 Observable을 구독하는 Observer가 존재하고, Observable이 순차적으로 발행하는 데이터에 대해서 반응함, 이는 Observer 패턴을 구현함

- 이 패턴을 사용함으로써 객체의 상태 변화를 관찰하는 관잘차 목록을 객체에 등록하고 상태 변화가 있을 때마다 메서드를 호출하여 객체가 직접 목록의 각 옵저버에게 변화를 알려줌

- 전체적인 흐름이 아래와 같이 진행된다고 볼 수 있음

![one](/cheewr85/img/fourteen.png)

- 이를 보면 시간순으로 데이터가 발행되는 것을 -> 로 표현을 한 것이고 그 안에 있는 도형들은 각각 Observable에서 발행하는 데이터이고 onNext 알림이 발생해서 데이터를 발행함

- 그리고 `|` 모양은 데이터 발행이 완료했다는 것을 의미함, 점선은 각 함수의 입력과 출력데이터이고 가운데 박스는 함수를 의미함, 이런 과정이 아래와 같이 더 복잡하게 발생할 수 있음

![one](/cheewr85/img/fifteen.png)

- 이를 코드적으로 파악을 더 해본다면 아래와 같이 볼 수 있음
```java
Observable.create(
  subscriber -> {
    try {
      List<Book> books = dao.findAll();
      if (!subscriber.isUnsubscribed()) {
        subscriber.onNext(books);
        subscriber.onCompleted();
      }
    } catch (Exception e) {
      if (!subscriber.isUnsubscribed()) {
        subscriber.onError(e);
      }
    }
  })
  .subscribeOn(Schedulers.io())
  .subscribe(books -> {
    // Next Step
  }, throwable -> {
    // Error handling
  });
```
- 즉, 도서정보를 가져오는 DAO를 비동기처리롤 가져오는 작업에 있어서 위에서 설명한 바와 같이 Observable을 통해서 비동기 작업을 처리할 수 있음

## Coroutine
- 비동기 구현에 있어서 Coroutine을 활용함으로써 위에서 말하는 Thread 처리보다 더 효과적으로 다룰 수 있음

- 이는 Coroutine을 실행 중인 Thread를 차단하지 않고 suspension을 하여 단일 Thread에서 많은 Coroutine을 실행할 수 있음, suspension을 통해 많은 동시 작업을 지원하면서 메모리 절약도 가능함

- 여기서 네트워크 요청을 아래와 같이 Coroutine을 사용하지 않고 쓴다면 아래와 같이 됨, 하지만 아래와 같이 처리할 경우 앞서 말했듯이 Thread를 차단하거나 대기를 하기 때문에 ANR 문제가 생김
```kotlin
sealed class Result<out R> {
    data class Success<out T>(val data: T) : Result<T>()
    data class Error(val exception: Exception) : Result<Nothing>()
}

class LoginRepository(private val responseParser: LoginResponseParser) {
    private const val loginUrl = "https://example.com/login"

    // Function that makes the network request, blocking the current thread
    fun makeLoginRequest(
        jsonBody: String
    ): Result<LoginResponse> {
        val url = URL(loginUrl)
        (url.openConnection() as? HttpURLConnection)?.run {
            requestMethod = "POST"
            setRequestProperty("Content-Type", "application/json; utf-8")
            setRequestProperty("Accept", "application/json")
            doOutput = true
            outputStream.write(jsonBody.toByteArray())
            return Result.Success(responseParser.parse(inputStream))
        }
        return Result.Error(Exception("Cannot open HttpURLConnection"))
    }
}
```
```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {
        val jsonBody = "{ username: \"$username\", token: \"$token\"}"
        loginRepository.makeLoginRequest(jsonBody) // 네트워크 요청을 보낸 순간 UI Thread를 차단함
    }
}
```

- 그래서 위의 Repository 클래스를 올바르게 이용하기 위해서 새로운 Coroutine을 만들어서 I/O Thread에 네트워크 요청을 할 수 있음
```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {
        // Create a new coroutine to move the execution off the UI thread
        viewModelScope.launch(Dispatchers.IO) {
            val jsonBody = "{ username: \"$username\", token: \"$token\"}"
            loginRepository.makeLoginRequest(jsonBody)
        }
    }
}
```

- 여기서 추가적으로 Repository 클래스에 대해서 이 coroutine 실행을 안전하게 처리하기 위해서 아래와 같은 조치를 할 수 있음
```kotlin
class LoginRepository(...) {
    ...
    suspend fun makeLoginRequest(
        jsonBody: String
    ): Result<LoginResponse> {

        // Move the execution of the coroutine to the I/O dispatcher
        return withContext(Dispatchers.IO) {
            // Blocking network request code
        }
    }
}
```

- 여기서 suspend 함수를 씀으로써 Coroutine을 무조건 실행해야함, 그래서 ViewModel 클래스에 대해서 기본 Thread 외부로 이동하고 이 함수의 Coroutine이 기본 Thread에서 실행될 수 있음
```kotlin
class LoginViewModel(
    private val loginRepository: LoginRepository
): ViewModel() {

    fun login(username: String, token: String) {

        // Create a new coroutine on the UI thread
        viewModelScope.launch {
            val jsonBody = "{ username: \"$username\", token: \"$token\"}"

            // Make the network call and suspend execution until it finishes
            val result = loginRepository.makeLoginRequest(jsonBody)

            // Display result of the network request to the user
            when (result) {
                is Result.Success<LoginResponse> -> // Happy path
                else -> // Show error in UI
            }
        }
    }
}
```

- 여기서 그러면 진행 과정은 View 레이어에서 login 함수를 호출함 그리고 launch가 기본 Thread에서 네트워크 요청을 보낼 새 Coroutine을 만들고 실행을 시작함

- 그런 다음 Coroutine에서 makeLoginRequest 부분에서 withContext 블록 실행이 끝날 때까지 Coroutine의 추가 실행을 suspend함

- 그리고 withContext 블록이 완료되면 login()의 Coroutine이 네트워크 요청의 결과와 함께 기본 Thread에서 실행을 재개함

- 예외처리를 try-catch 문처럼 launch-catch로 처리할 수 있음

- 세부적으로 핸들링 하는 과정은 공식문서 세부 참조
- https://developer.android.com/kotlin/coroutines/coroutines-adv

### Coroutine Flow
- 이 Flow는 위와 같이 suspend를 활용하여 단일 값만 반환하는 것과 달리 여러 값을 순차적으로 내보낼 수 있는 유형임

- Coroutine을 기반으로 빌드되면서 여러 값을 제공할 수 있음, 데이터 스트림의 개념이므로 내보낸 값은 동일한 유형이어야함

- 값을 비동기적으로 생성하고 사용할 수 있음, 데이터 스트림은 아래와 같이 세 가지 항목이 있음

![one](/cheewr85/img/sixteen.png)

- 이러한 방법은, 앞서 본 Coroutine과 다르게 쓰는데, 데이터베이스에서 실시간 업데이트를 수신하는 등의 방식으로써 사용함
