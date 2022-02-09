# ViewModel & LiveData

## ViewModel

- 수명 주기를 고려하여  **UI 관련 데이터**를 저장, 관리

- 화면 회전과 같은 구성 변경 시에도 데이터를 유지

- **Activity와 Fragment는 UI 컨트롤러**로서  수명 주기 보유

- 화면이 회전되는 경우처럼 구성이 변경되면(configuration change) Activity와 Fragment는 재생성됨

- Activity와 Fragment가 재생성 되면 UI 컴포넌트는 물론이고 관련 UI에 들어가는 데이터 역시 다시 할당해주어야 함

- 데이터가 단순하면 onSaveInstanceState() 메서드를 호출하여 Bundle 데이터를 복원

- 소량의 단순 데이터가 아니면 UI 컨트롤러 상에서 비동기 호출을 해야 함

- 비동기 호출은 유지 관리가 필요하고, 화면 회전 등의 이유로 클래스가 재생성될 때 이미 수행된 호출을 반복해야하므로 리소스가 낭비됨

- UI 컨트롤러는 UI 데이터를 **표시**하고, 사용자의 작업에 반응하며, 권한 요청 등의 운영체제 커뮤니케이션을 처리하도록 하고, **뷰 데이터 소유권**은 ViewModel이 담당하는 것이 효율적

### ViewModel 구현

- AAC ViewModel은 UI 컨트롤러에 ViewModel 도우미 클래스를 제공

- UI 컨트롤러에서 필요한 뷰 데이터는 ViewModel에 보관하며, UI 컨트롤러 인스턴스에서 즉시 사용 가능

```kotlin
class MyViewModel : ViewModel() {
    private val users: MutableLiveData<List<User>> by lazy {
        MutableLiveData<List<User>>().also {
            loadUsers()
        }
    }

    fun getUsers(): LiveData<List<User>> {
        return users
    }

    private fun loadUsers() {
        // user 데이터를 비동기로 패
    }
}
```

```kotlin
class MyActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        // ON_CREATE 상태에서 ViewModel 생성
        // 액티비티가 재생성될 때는 이미 생성된 ViewModel 객체 이용 
        // 'by viewModels()' 코틀린 프라퍼티 위임 사
        // from the activity-ktx artifact
        val model: MyViewModel by viewModels()
        model.getUsers().observe(this, Observer<List<User>>{ users ->
            // update UI
        })
    }
}
```

- ViewModel 객체는 뷰와 LifecycleOwners의 특정 인스턴스화보다 오래 지속

- LiveData 객체를 직접 관찰하지 않고 LifecycleObserver를 이용

![활동 상태 변경에 따라 ViewModel의 수명 주기를 설명합니다.](https://developer.android.com/images/topic/libraries/architecture/viewmodel-lifecycle.png)

- 화면 회전 등 Configuration Change에 의한 onDestory 콜백이 아닌 finish()에 의한 onDestroy 콜백에 반응하여 onCleared() 함수를 통해 ViewModel을 메모리에서 제거

### ViewModel로 로더 대체

- 로더를 통해 데이터베이스의 내용을 관찰하고 값이 변경되면 로더가 자동으로 데이터를 새로고침 트리거 후 UI에 데이터 업데이트

- Room DB와 LiveData를 활용하여, 데이터 변경시 Room에서 LiveData에 변경 사항 전달 -> LiveData가 수정된 데이터로 UI 업데이트

![](https://developer.android.com/images/topic/libraries/architecture/viewmodel-replace-loader.png)

## LiveData

- 관찰 가능한 데이터 홀더 클래스

- Activity, Fragment, Service 등의 수명 주기를 고려하여 활성 상태에만 데이터 업데이트

- 관찰자는 Observer 클래스를 통해 표현되는데, 이 관찰자의 수명 주기가 STARTED 또는 RESUMED 상태일 때 LiveData가 관찰자를 활성 상태로 간주

- LifecycleOwner 인터페이스를 구현하면 관찰자의 상태가 DESTROYED로 변경될 때 관찰자를 삭제할 수 있음 -> Lifecycle 관찰 중단 -> 리소스 누스 방지

#### LiveData 사용의 이점

- UI - Data 상태 일치
  데이터가 변경되면 Observer 객체를 통해 UI를 업데이트. 데이터가 변경될 때마다 데이터를 UI에 연결시켜줄 필요가 없음

- 메모리 누수 방지
  관찰자가 Lifecycle 객체에 결합되어 있어, 수명 주기가 끝나면 자동으로 삭제

- 액티비티가 백 스택으로 가는 등 수명주기가 비활성화 되면 관찰자-LiveData 소통 중단

- 수명주기를 알아서 처리해줌
  UI 컨트롤러가 데이터를 관찰하도록 하면 LiveData가 관련 수명 주기 상태 변경을 인식하고 관리해줌

- 기기 회전과 같은 구성 변경 시 UI 컨트롤러가 다시 생성되면 즉시 최신 데이터 업데이트

### Room + LiveData

- Room 라이브러리는 관찰 가능한 쿼리를 지원하고, 이 쿼리가 LiveData 객체를 반환
  이때 쿼리는 DAO에서 작성됨

- 데이터베이스가 업데이트 될 때 Room에서 LiveData 객체 업데이트에 필요한 모든 코드를 생성 -> 백그라운드 스레드에서 비동기적으로 쿼리 실행

### Example Code

```kotlin
class MainActivity : AppCompatActivity() {
    lateinit var ViewModel: FragmentChatViewModel
}

override fun onCreate(savedInstanceState : Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    val repository = PaperPlaneRepository(PaperPlaneDatabase(this))
        val factory = FragmentChatViewModelFactory(repository)
        viewModel = ViewModelProvider(this, factory)[FragmentChatViewModel::class.java]
        
    // 업데이트된 데이터를 RecyclerView 어댑터에 넣어줄 때 AsyncDiffer를 이용
    viewModel.allMyPaperPlaneRecord(UID).observe(viewLifecycleOwner, Observer {
            sentPlaneAdapter.differ.submitList(it)
            tv_delete_all_records.isEnabled = it.isNotEmpty()
            rv_sent_paper.scrollToPosition(sentPlaneAdapter.itemCount-1)
        })
}
```

```kotlin
class PaperPlaneRepository(private val db: PaperPlaneDatabase) {
    suspend fun insert(user : User) = db.getUserInfoDao().insert(user)
    suspend fun getUser(uid : String) = db.getUserInfoDao().getUserInfo(uid)
    suspend fun delete(user: User) = db.getUserInfoDao().delete(user)
    suspend fun isExists(uid : String) = db.getUserInfoDao().isExists(uid)
}
```

```kotlin
class FragmentChatViewModelFactory(private val repository: PaperPlaneRepository): ViewModelProvider.NewInstanceFactory() {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        return FragmentChatViewModel(repository) as T
    }
}
```

```kotlin
class FragmentChatViewModel(private val repository: PaperPlaneRepository) : ViewModel() {

    // In coroutines thread insert item in insert function.
    fun insert(item: FirstPaperPlanes) = CoroutineScope(IO).launch {
        repository.insert(item)
    }
}
```

![](https://miro.medium.com/max/700/0*Io9CAKKPaZbZH1Q0.png)
