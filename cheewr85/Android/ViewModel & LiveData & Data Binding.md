# ViewModel
- 수명 주기를 고려하여 UI 관련 데이터를 저장하고 관리하도록 설계됨, 화면 회전과 같이 구성을 변경할 때도 데이터를 유지할 수 있음

- 일반적으로 Activity나 Fragment에서 수명 주기에 따라서 데이터가 삭제되곤 함, 이때 일반적으로 onSaveInstanceState 메서드를 사용해서 onCreate의 번들에서 데이터를 복원했음

- 하지만 이런 접근법은 소량의 데이터에만 적합함, 그리고 이런식으로 데이터 반환에 있어서 비동기 호출을 관리해야 하는 점, 많은 유지관리와 리소스 낭비가 있다는 점이 있음

- 이런 부분을 방지하고자 ViewModel을 사용하여서 이런 데이터 관리를 적절하게 처리할 수 있음

## LiveData
- ViewModel에 디테일하게 보기 앞서 ViewModel 예제 코드에서 LiveData를 주로 다루기 때문에 별도로 알아볼 것임

- LiveData의 경우 일반 클래스와 다르게 수명 주기를 인식함, 즉, Activity, Fragment, Service 등 다른 앱 구성요소의 수명 주기를 고려함

- 수명 주기 인식을 통해 LiveData는 Activity 수명 주기 상태에 있는 앱 구성요소 Observer만 업데이트함

- 이러한 LiveData를 사용함으로써, UI와 데이터 상태의 일치 보장, 메모리 누수를 방지하며 중지된 Activity로 인한 비정상 종료도 없고 수명 주기를 수동으로 처리할 필요도 없으며 최신 데이터 유지, 적절한 구성 변경 즉, 기기 회전 프래그먼트 재생성 등 사용 가능한 최신 데이터를 즉시 받을 수 있고 리소스 공유도 가능하게 함

- 이러한 LiveData 사용을 하려면 일반적으로 ViewModel 클래스 내에서 이루어짐, 그래서 ViewModel을 자세히 보기 앞서 설명하는 것임


### 예시(LiveData)
- LiveData는 특정 유형의 데이터를 보유할 인스턴스로 생성가능, 일반적으로 ViewModel에서 이루어짐

- onChanged 메소드를 정의하는 Observer 객체 만듬, 이 메소드는 LiveData 객체가 보유한 데이터 변경 시 발생하는 작업을 제어함, Activity나 Fragment에 Observer 객체를 만듬 

- observer 메소드를 사용하여 LiveData 객체에 Observer 객체를 연결함, observer 메소드는 LifecycleOwner 객체를 사용함

- 이러면 Observer 객체가 LiveData 객체를 보면서 변경사항에 관한 알림을 받음, Activity나 Fragment에 Observer 객체를 연결함, 이 과정에 맞게 설정해주면 됨

- LiveData는 List와 같은 객체를 비롯하여 모든 데이터와 함께 사용할 수 있음, 일반적으로 ViewModel 객체 내에 저장되어 아래와 같이 getter 메소드를 통해 액세스 됨
```kotlin
class NameViewModel : ViewModel() {

    // Create a LiveData with a String
    val currentName: MutableLiveData<String> by lazy {
        MutableLiveData<String>()
    }

    // Rest of the ViewModel...
}
```

- 그리고 onCreate 메소드에서 LiveData에 대한 객체를 Observer 세팅과 함께 확인할 수 있음

- nameObserver를 매개변수로 전달하여 observe를 호출하여 LifecycleOwner로 전달을 함

- 이 때 onChanged가 즉시 호출되어 저장된 최신값을 제공함
```kotlin
class NameActivity : AppCompatActivity() {

    // Use the 'by viewModels()' Kotlin property delegate
    // from the activity-ktx artifact
    // ViewModel 세팅
    private val model: NameViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Other code to setup the activity...

        // Create the observer which updates the UI.
        val nameObserver = Observer<String> { newName ->
            // Update the UI, in this case, a TextView.
            nameTextView.text = newName
        }

        // Observe the LiveData, passing in this activity as the LifecycleOwner and the observer.
        model.currentName.observe(this, nameObserver)
    }
}
```

- 여기서 업데이트를 하는데 있어서 MutableLiveData의 경우 setValue와 postValue 메소드를 통해서 LiveData 객체에 저장된 값을 수정할 수 있음
```kotlin
button.setOnClickListener {
    val anotherName = "John Doe"
    model.currentName.setValue(anotherName)
}
```

- 이런 특징을 활용하여서 Room과 연동, 코루틴과 함께 사용 그리고 아키텍처 측면에서 LiveData 특성상 수명 주기를 따를 수 있기 때문에 수명이 다른 객체 간에 통신이 가능함

- 이는 UI Controller는 오직 data를 display하게끔 해주는 역할로써도 더 부각시켜줄 수 있음, 즉 일반적으로 아는 수준에서의 Fragment와 Activity에서 모든 동작과 이벤트 처리뿐 아니라 그 클래스에서 데이터도 관리를 하고 처리하는데 이 부분에 대해서 분리를 시켜서 관리를 좀 더 용이하게 해 줄 수 있는 것임

## 예시(ViewModel)
- 앞서, LiveData에서 봤지만 ViewModel을 좀 더 알아보면 ViewModel 객체가 구성이 변경되는 동안 자동으로 보관되어 객체가 보유한 데이터는 다음 Activity 또는 Fragment 인스턴스에서 즉시 사용할 수 있음
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
        // Do an asynchronous operation to fetch users.
    }
}
```

- 그리고 해당 ViewModel을 Activity에서 액세스 할 수 있음
```kotlin
class MyActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        // Create a ViewModel the first time the system calls an activity's onCreate() method.
        // Re-created activities receive the same MyViewModel instance created by the first activity.

        // Use the 'by viewModels()' Kotlin property delegate
        // from the activity-ktx artifact
        val model: MyViewModel by viewModels()
        model.getUsers().observe(this, Observer<List<User>>{ users ->
            // update UI
        })
    }
}
```

## ViewModel의 수명 주기
- 이러한 ViewModel은 ViewModel을 가져올 때 ViewModelProvider에 전달되는 LifeCycle로 지정됨

- Activity에서 Activity까 끝날 떄까지 Fragment에서 Fragment가 분리될 때까지 메모리에 남아있음

![one](/cheewr85/img/android/ten.png)

- 즉, ViewModel이 처음 요청되었을 때부터 Activity가 끝나고 폐기될 때까지 ViewModel은 존재함

- 그래서 이런 부분에 있어서 Activity, Fragment, Context 참조를 유의해야함

### ViewModel 추가
- 이러한 ViewModel의 특성을 바탕으로 Fragment간 데이터 공유에 있어서 유용하게 쓸 수 있음

- 앱 UI의 데이터와 데이터베이스 간의 동기화를 유지하는데도 있어서 ViewModel을 사용헤서 데이터 로드 작업을 분리할 수 있음

## Data Binding
- 선언적 형식으로 레이아웃의 UI 구성요소를 앱의 데이터 소스와 결합할 수 있는 지원 라이브러리

- 즉, xml 파일에 Data를 연결해서 사용할 수 있게 해주는 것, 아래와 같이 레이아웃 파일에서 직접 View에 할당을 해주는 것
```xml
<TextView
        android:text="@{viewmodel.userName}" />
```

- 이외에도 레이아웃의 뷰를 데이터 개체와 결합하는데 필요한 클래스를 자동으로 생성함, 기존 레이아웃과 공존하면서 data 요소 내의 저장하여서 layout 태그로 래핑할 수 있음

- data, variable 태그를 추가하고 name에는 변수명을, type에는 데이터 바인딩을 통한 이벤트를 세팅할 것을 적어주면 됨

### 예시
- MainActivity에서 TextView만 Button을 추가한 ConstraintLayout이 아래와 같음
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout     xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/hello_text_view"
        android:text="Hello!"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"/>
  
    <Button
        android:id="@+id/btn_change"
        android:text="Text Change!"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/hello_text_view"
        app:layout_constraintBottom_toBottomOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

- 여기서 최상단 ConstraintLayout을 layout아래에 둠 이때 data, variable 추가함
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
  
    <data>
      <variable
         name="main"
         type="com.joel.jojo.MainActivity"/>
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

        <TextView
            android:id="@+id/hello_text_view"
            android:text="Hello!"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"/>
      
        <Button
            android:id="@+id/btn_change"
            android:text="Text Change!"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/hello_text_view"
            app:layout_constraintBottom_toBottomOf="parent"/>

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

- 그런 다음 세팅한 xml의 액티비티 클래스 파일로 이동하면 직접적으로 데이터 변화를 처리할 수 있음
```kotlin
class MainActivity : AppCompatActivity() {
    // xml 파일명이 카멜케이스로 클래스가 자동생성 됩니다.
    private lateinit var binding: ActivityMainBinding

    var text = "Hello!"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // binding 세팅
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        // 현재 binding시킨 xml의 variable name
        binding.main = this
        // binding 버튼 클릭 이벤트
        binding.btnChange.setOnClickListener {
            text = "Hello Binding!"
            // Data가 변동될 경우 binding된 View들에 Data 변화를 알려줌
            binding.invalidateAll()
        }
    }
}
```

- xml에 데이터 집합 클래스를 bind해서 해당 클래스가 변경되면 연결된 여러개의 View가 한 번에 변경할 수 있음

- BindingAdapter를 통해서 쉽게 출력하고 정리할 수 있음, LiveData를 사용하여서 Data 반영시 View도 같이 변경을 할 수 있음

- findViewById를 호출하지 않아도 자동으로 xml에 있는 View들을 만들어줌

- RecyclerView에 각각의 item을 set 해주는 작업을 자동으로 해줄 수 있음

- data가 바뀌면 자동으로 View를 변경할 수 있고, xml 리소스만 보고도 View에 어떤 데이터가 들어가는지 파악이 가능함

- 코드 가독성이 좋아지고 코드량도 줄어들 수 있음(MVP, MVVM 패턴을 구현하기 위해서 유용하게 쓰임)