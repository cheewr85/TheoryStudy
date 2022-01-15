# Android Activity Lifecycle

- 사용자가 앱과 상호작용하면서 액티비티 인스턴스의 상태는 계속 변화(액티비티 생성, 중단, 재시작, 종료 등) 이러한 변화의 과정을 **수명주기**라고 함. 

- 액티비티 클래스는 액티비티의 상태 변화에 따라 작동하는 콜백 메서드를 제공. 앱의 상태에 따라 앱 작동 방식을 이 메서드에 선언 

### 액티비티 수명 주기 이해를 통해 다뤄줘야 할 문제

- 앱 사용 도중 전화가 오거나 다른 앱으로 전환되면서 앱이 비정상 종료되는 문제

- 앱이 사용되지 않는 동안 리소스가 낭비되는 문제

- 앱에서 잠시 나갔다가 다시 돌아왔을 때, 이전의 상태가 저장되지 않는 문제

- 화면이 회전할 경우, 비정상 종료되거나 이전의 상태가 저장되지 않는 문제

### 적절한 수명 주기 활용 예시

1. 스트리밍 동영상 플레이어 빌드 시, 사용자가 다른 앱으로 전환하면 동영상을 일시중지하고 네트워크 연결 종료

2. 사용자가 돌아오면 네트워크를 다시 연결하고, 일시중지한 시점부터 영상을 재생할 수 있도록 허용

## 안드로이드 수명 주기 콜백 메서드

![](https://developer.android.com/guide/components/images/activity_lifecycle.png?hl=ko)

### 핵심 메서드 6가지

1. onCreate()
   
   - 액티비티 수명 주기 동안 **단 한 번만** 발생해야 하는 로직을 담당
     ex) 데이터가 리스트에 바인딩, activity와 ViewModel의 연결, 클래스 범위 변수의 인스턴스화
   
   - savedInstanceState 매개 변수를 수신하여 활동의 이전 저장 상태가 포함된 Bundle 객체를 전달 받음. 처음 생성된 활동이면, Bundle == null
   
   ```onCreate()
   lateinit var textView: TextView
   
   // some transient state for the activity instance
   var gameState: String? = null
   
   override fun onCreate(savedInstanceState: Bundle?) {
       // 부모 클래스의 onCreate 함수를 호출하여 액티비티 생성 완결
       super.onCreate(savedInstanceState)
   
       // 미리 저장된 인스턴스 상태를 변수에 할당
       gameState = savedInstanceState?.getString(GAME_STATE_KEY)
   
       // resource 폴더에 준비된 UI layout 파일을 액티비티에 연결
       setContentView(R.layout.main_activity)
   
       // 변수의 초기
       textView = findViewById(R.id.text_view)
   }
   
   // 이 콜백 메서드는 onSaveInstanceState() 메서드를 통해 이전 저장된 인스턴스가
   // 있어야만 호출됨. 이렇게 다 onCreate()에서 어떠한 상태를 복구시킴 
   override fun onRestoreInstanceState(savedInstanceState: Bundle?) {
       textView.text = savedInstanceState?.getString(TEXT_VIEW_KEY)
   }
   
   // invoked when the activity may be temporarily destroyed, save the instance state here
   override fun onSaveInstanceState(outState: Bundle?) {
       outState?.run {
           putString(GAME_STATE_KEY, gameState)
           putString(TEXT_VIEW_KEY, textView.text.toString())
       }
       // call superclass to save any view hierarchy
       super.onSaveInstanceState(outState)
   }
   ```

2. onStart()
   
   - 액티비티가 생성되고 나서, **시작된 직후**에 호출되는 메서드
   
   - 아직 사용자에게 액티비티의 UI가 **보이지 않음**
   
   - 뷰가 세팅된 액티비티를 포어그라운드로 내보낼 준비를 하는 단계
   
   - UI와 관련된 코드를 여기서 관리
   
   - 메서드가 완료되면 곧바로 onResume() 콜백 호출

3. onResume()
   
   - 이름이 나타내듯, 액티비티가 재개되어야만 호출되는 것이 아니라, 처음 실행되었어도 onStart 이후 앱이 사용자와 상호작용하기 직전 호출
   
   - 이제 UI는 사용자에게 **보이고 있는 상태**
   
   - 전화가 오거나, 다른 앱이 실행되거나, 화면이 꺼질 경우 호출됨
   
   - **리소스 관리**를 위해 onPause() 메서드에서 해제되는 요소들을 다시 초기화하는 단계
     
     ```onResume()
     class CameraComponent : LifecycleObserver {
     
         ...
         // @OnLifecycleEvent와 같이 @ 기호가 붙는 방식은 직접 작성해서 사용해본적은 없는데,
         // 자바의 Annotation과 같은 역할로, 실제 작동되는 부분은 아니지만 주석대로
         // 코드가 작성되지 않으면 컴파일 오류가 출력된다.
         // 하기 코드는 주석을 통해 수명 주기 이벤트에 사용되는 메서드라는 것을 명시하고 있다.
         // LifeCycleObserver가 현재 액티비티의 수명 주기를 판단한다.
         @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
         fun initializeCamera() {
             if (camera == null) {
                 getCamera()
             }
         }
     
         ...
     }
     ```

4. onPause()
   
   - 사용자가 액티비티를 떠날 경우 호출되는 메서드, 즉 포어그라운드에 있지 않음
     멀티 윈도우 모드에서 앱이 실행될 경우, 다른 앱이 포커스를 가지고 있는 경우
     모달 창이 열려 액티비티가 여전히 보이지만 포커스 상태는 아닌 경우
   
   - 시스템 리소스, 센서 핸들(ex. GPS) 등 모든 리소스를 해제할 경우 이곳에서
   
   - 앱이 멀티 윈도우 모드로 사용되는 경우 여전히 UI가 비춰지고 있을 수 있으므로 UI 관련 리소스를 완전히 해제하거나 조정할 경우 onPause() 대신 onStop()에서 처리
     
     ```onPause()
     class CameraComponent : LifecycleObserver {
     
         ...                
     
         // LifeCycleObserver가 ON_PAUSE 이벤트를 수신하 카메라 관련 리소스 해제
         @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
         fun releaseCamera() {
             camera?.release()
             camera = null
         }
     
         ...
     }
     ```

5. onStop()
   
   - 액티비티가 백그라운드로 넘어가면 onStop() 콜백이 호출됨
   
   - 리소스 해제
   
   - 액티비티 객체가 메모리에 체류
   
   - 액티비티 객체가 모든 상태와 멤버 변수를 관리하지만 창 관리자와는 연결 X
   
   - 액티비티가 다시 시작되면 onResume() 메서드가 호출되면서 객체 안 정보들 역시 호출
   
   - CPU가 많이 사용되는 종료 작업도 여기서 담당
     
     ```onPause()
     override fun onStop() {
         super.onStop()
     
         // UI 리소스가 해제되므로 UI로부터 데이터를 가져와 저장
         val values = ContentValues().apply {
             put(NotePad.Notes.COLUMN_NAME_NOTE, getCurrentNoteText())
             put(NotePad.Notes.COLUMN_NAME_TITLE, getCurrentNoteTitle())
         }
     
         // 백그라운드에서 SQLite 비동기 쿼리 핸들러 작업 시작
         asyncQueryHandler.startUpdate(
                 token,     // int token to correlate calls
                 null,      // cookie, not used here
                 uri,       // The URI for the note to update.
                 values,    // The map of column names and new values to apply to them.
                 null,      // No SELECT criteria are used.
                 null       // No WHERE columns are used.
         )
     }
     ```

6. onDestroy()
   
   - 액티비티가 소멸되면서 호출되는 콜백 메서드
     
     - 액티비티의 finish()가 호출되어 활동이 종료되는 경우
     
     - 기기 회전 또는 멀티 윈도우 모드로 인해 시스템이 일시적으로 액티비티 소멸
   
   -  ViewModel을 사용하는 경우, Activity가 재사용되지 않는다면  액티비티 소멸 전 ViewModel 객체를 제거해야 함
   
   - onStop()에서 처리되지 않은 나머지 리소스를 모두 해제해야 함
   
   - Activity.isFinishing() : 앱 내에서 액티비티 finish()가 호출되어 종료되고 있는 것인지, 외부로부터 종료 요청이 들어온 것인지 확인
     
     >  If the activity is finishing, returns true; else returns false.

![](C:\Users\Sejin\AppData\Roaming\marktext\images\2022-01-07-16-15-47-image.png)

#### anyActivity.isFinishing() == false 인 경우

- 메모리 공간이 부족할 경우 시스템은 액티비티가 직접 finish() 호출하도록 하지 않고, 액티비티를 실행하는 프로세스를 종료하여 해당 액티비티 외에 모든 작업을 함께 소멸시킴

- 시스템 설정에서 애플리케이션 관리자를 이용해서도 앱을 종료시킬 수 있음

## 임시 UI 상태 저장 및 복원

- 화면이 회전되거나 멀티 윈도우 모드로 진입 시 onDestroy() 메소드가 호출되어 모든 UI 상태를 제거함

- 사용자가 액티비티 사용을 중단한 상태에서 시스템이 애플리케이션이 프로세스를 소멸시킬 수도 있음

- 좋은 UX를 제공하기 위해서 ViewModel이나 onSaveInstanceState(), 로컬 저장소 등을 활용하여 UI를 보존해야 함

- int, boolean 등의 원시 데이터 유형 또는 간단한 객체(String)와 같은 가벼운 UI 데이터를 보존할 때는 onSaveInstanceState()만으로도 충분함

- 그러나 보통은 onSaveInstanceState()가 직렬화/역직렬화 비용을 발생시키므로, ViewModel까지 함께 사용하는 것이 일반적
  
  > 직렬화 : 객체들의 데이터를 연속적인 데이터로 변형하여 Stream을 통해 데이터를 읽도록 해줌
  > 
  > 역직렬화 : 직렬화된 파일 등을 역으로 직렬화하여 다시 객체의 형태로 만드는 것을 의미. 전송된 스트림 데이터를 읽어 원래 객체의 형태로 복원

##### Instance state (인스턴스 상태)

- 정상적인 앱 동작으로 finish() 메서드가 호출되어 앱이 종료되면 추가적인 작업 필요 X

- 시스템 제약(구성 변경 또는 메모리 부족) 등으로 액티비티가 소멸될 경우, 실제 액티비티 인스턴스가 사라져도 시스템에 존재했던 정보는 남아있기 때문에, 사용자가 액티비티로 돌아갈 때, 소멸 당시의 액티비티 상태를 활용하여 새로운 인스턴스를 생성함

- 이때 활용되는 데이터가 **Instance state**이고 , 키-값 쌍의 컬렉션의 Bundle 객체

- Bundle Instance를 사용하여 액티비티 레이아웃의 View Instance 관련 정보를 저장 (EditText 위젯에 입력된 텍스트 값) -> 액티비티 인스턴스가 재생성되면 활용
  
  > sharedPreference와 구조적인 차이는 없고 기능적인 차이만 있는것인지?

- Bundle Instance는 메인 스레드에서 직렬화되어야 하고 시스템  프로세스 메모리를 사용해서 소량의 데이터에만 적합

```onSaveInstanceState
override fun onSaveInstanceState(outState: Bundle?) {
    // 현재 UI 데이터를 저장
    outState?.run {
        putInt(STATE_SCORE, currentScore)
        putInt(STATE_LEVEL, currentLevel)
    }

    // 항상 부모 클래스를 호출해서 뷰 계층 구조 상태를 저장하도록 해야 함 
    super.onSaveInstanceState(outState)
}
    
companion object {
    val STATE_SCORE = "playerScore"
    val STATE_LEVEL = "playerLevel"
}
```

**사용자가 액티비티를 명시적으로 닫거나, finish()가 호출되어 액티비티가 소멸되는 경우에는 onSaveInstanceState() 호출 X**

### 저장된 인스턴스 상태로 액티비티 UI 복원

- 액티비티가 소멸됐다가 재생성되면, Bundle을 통해 인스턴스 상태를 복구

- onCreate()와 더불어 onRestoreInstanceState() 콜백 메서드 모두 Bundle을 수신하여 상태 복구

- onCreate()가 호출한 번들 상태가 null이면, 새 인스턴스가 생성
  
  ```savedInstanceState
  override fun onCreate(savedInstanceState: Bundle?) {
      super.onCreate(savedInstanceState) // Always call the superclass first
  
      // 소멸된 인스턴스를 재생성 하고 있는 것인지 확인
      if (savedInstanceState != null) {
          with(savedInstanceState) {
              // Restore value of members from saved state
              currentScore = getInt(STATE_SCORE)
              currentLevel = getInt(STATE_LEVEL)
          }
      } else {
          // 새롭게 액티비티 객체를 생성하는 것일 경우 변수 초기
      }
      // ...
  }
  // 하기 메서드는 복원할 인스턴스 상태가 있을 경우에만 호출되므로 Bundle의 null 체크 필요X
  override fun onRestoreInstanceState(savedInstanceState: Bundle?) {
      super.onRestoreInstanceState(savedInstanceState)
  
      savedInstanceState?.run {
          currentScore = getInt(STATE_SCORE)
          currentLevel = getInt(STATE_LEVEL)
      }
  }
  ```

### 액티비티 간 이동

- Intent 객체를 통해 새로운 액티비티를 시작함

- 소량의 데이터는 Intent를 통해 다른 액티비티로 전달
  
  ### startActivity()
  
  - 새로 시작될 액티비티가 결과를 반환하지 않는다면 startActivity로 액티비티 시작
  
  - 명시적 인텐트 vs 암시적 인텐트
    
    -> 앱 내 액티비티 실행 vs 다른 앱의 액티비티 활용
  
  - 인텐트를 통해서 가벼운 데이터 전송
    
    ```
    // 동반 데이터 없이 단순히 새로운 액티비티로 이동
    
    val intent = Intent(this, SignInActivity::class.java)
    startActivity(intent)
    ```

    ------------------------------------------------------------------
    // putExtra 메서드로 인텐트에 담을 데이터 할당
    
    val intent = Intent(Intent.ACTION_SEND).apply {
        putExtra(Intent.EXTRA_EMAIL, recipientArray)
    }
    startActivity(intent)
    ```

### startActivityForResult()

- 액티비티가 종료될 경우 결과를 반환받음
  ex. 연락처 목록 액티비티에서 선택된 사람의 데이터를 반환

- requestCode를 통해 식별된 활동을 onActivityResult(requestCode : Int, resultCode : Int, intent: Intent) 메서드로 결과 반환

- 시작된 하위 액티비티가 Intent 객체로 결과를 반환하면 onActivityResult()가 수신
  
  ```startActivityForResult
  class MyActivity : Activity() {
    // ...
  
    override fun onKeyDown(keyCode: Int, event: KeyEvent?): Boolean {
        if (keyCode == KeyEvent.KEYCODE_DPAD_CENTER) {
            // 아이템의 가운데를 누르면 해당 값을 변수에 할당
            startActivityForResult(
                    Intent(Intent.ACTION_PICK,Uri.parse("content://contacts")),
                    PICK_CONTACT_REQUEST)
            return true
        }
        return false
    }
  
    override fun onActivityResult(requestCode: Int, resultCode: Int, intent: Intent?) {
        when (requestCode) {
            PICK_CONTACT_REQUEST ->
                if (resultCode == RESULT_OK) {
                    startActivity(Intent(Intent.ACTION_VIEW, intent?.data))
                }
        }
    }
  
    companion object {
        internal val PICK_CONTACT_REQUEST = 0  // requestCode
    }
  }
  ```

### 액티비티간 조정

- 한 액티비티에서 다른 액티비티로 이동하면 두 액티비티 모두 수명주기 전환

- 새로운 액티비티가 생성되는 동안 기존의 액티비티는 ON_PAUSE 또는 ON_STOP 상태

- 새로운 액티비티가 완전히 생성되기 전까지 **기존 액티비티는 중단 X**

- 새로운 액티비티의 start **!=** 기존 액티비티의 destroy

###### 액티비티 A -> B 전환 프로세스

1. 액티비티 A : onPause() 콜백 호출

2. 액티비티 B : onCreate(), onStart(), onResume() 콜백 순차적 실행 (일반적인 LifeCycle)

3. 액티비티 A가 화면에 표시되지 않을 때 onStop() 
