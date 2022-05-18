# Fragment와 통신
- 이벤트에 올바르게 공유하거나 상태 정보를 공유하려면 Activity와 Activity의 Fragment 간 또는 두 개 이상의 Fragement간에 통신 채널이 있어야 할 때가 많음

- Fragment를 독립적으로 유지하려면 Fragment가 다른 Fragment 또는 호스트가 되는 Activity와 직접 통신이 안됨

- 이 때 ViewModel과 Fragment Result API라는 두 가지 통신 옵션을 활용하여서 통신을 할 수 있음

- 영구 데이터를 활용해서 처리하기 위해서는 ViewModel을 사용해야 하고 일회성 결과의 경우 Fragment Result API를 사용함

## 데이터 전달
- ViewModel과 Result API를 보기 앞서서 공식 문서상에서 언급한 것 외에 기본적으로 전달할 수 있는 방법을 알 수 있음

- Bundle을 통해서 Data를 주고 받을 수 있음, 이 Bundle은 key-value 상으로 마치 Intent에서 데이터를 전달하듯이 전달 할 수 있음

### 예시
- 아래와 같이 Bundle을 통해서 데이터를 넣어서 FragmentManger를 통해 Transaction 단위로 넘겨서 데이터 전달
```kotlin
//전송하려는 Fragment Class
//PassBundleFragment는 본인이 전달하고자 하는 Fragment class
 val bundle = Bundle()
 bundle.putString("key", "value")
 
val passBundleBFragment = PassBundleBFragment()
passBundleBFragment.arguments = bundle parentFragmentManager.beginTransaction()
	.replace(R.id.fragment_container_bundle, PassBundleFragment())
	.commit()
```
```kotlin
// 받을 Fragment Class
override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
	var result = arguments?.getString("key")
	
    	return inflater.inflate(R.layout.fragment_pass_b, container, false)
    }
```

- 그리고 Activity와 Fragment 사이에서도 이러한 데이터 전달을 결과/요청 코드로 주고받을 수 있음

- 데이터만 전달하는 경우 혹은 이동한 Activity 또는 Fragment에서 결과를 받아 다시 돌아와서 작업하는 경우가 이에 해당함

- Intent를 활요해서 데이터를 전달해 줄 수 있음

### 예시
- 이 과정은 Fragment/Activity -> Activity로의 전달에 활용할 수 있음
```kotlin
// 제일 단순하고 쉬운 방법
val intent = Intent(this@Intent1,Intent2::class.java)
intent.putExtra("num1",1) //데이터 넣기
intent.putExtra("num2",2) //데이터 넣기
startActivityForResult(intent,101)
// 이동한 activity에서 추후 받아올 데이터가 없는
// 단순 데이터 전달 및 이동의 경우
// startActivity(intent)
```
```kotlin
//두번째 방법
val intent = Intent(this@Intent1,Intent2::class.java)
//코틀린의 유용한 기능, apply
intent.apply {
	this.putExtra("num1",1) // 데이터 넣기
   	this.putExtra("num2",2) // 데이터 넣기
}
startActivityForResult(intent,101)
```
```kotlin
//Intent2에서 데이터 받기
 //마지막 인자로 defaultValue를 꼭 넣어줘야함 null일수 있으니!
val number1 = intent.getIntExtra("num1", 0)
val number2 = intent.getIntExtra("num2", 0)
```
```kotlin
// 결과 코드를 가지고 Intent1로 이동하기
val resultIntent = Intent()
resultIntent.putExtra("result","333")
setResult(Activity.RESULT_OK,resultIntent)
//액티비티 종료! -> 이전의 Activity인 Intent1로 이동됨
finish()
```
```kotlin
//Intent1에서 데이터 받기
// requestCode : (1)에서 보내줬던 요청 코드
// resultCode : Intent2에서 보낸 결과 코드
// data : Intent2에서 보낸 데이터
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
super.onActivityResult(requestCode, resultCode, data)
        if(resultCode==Activity.RESULT_OK){
        	val result = data.getIntExtra("result",0)
            	Log.d("리턴값",""+result)
        }
    }
```

- 이를 Fragment 사이에서도 전달이 가능함
```kotlin
// Fragment1에서 Fragment2로 데이터 가지고 이동
var fragment2 = Fragment2()
var bundle = Bundle()
bundle.putInt("num1",1)
bundle.putInt("num2",2)
fragment2.arguments = bundle //fragment의 arguments에 데이터를 담은 bundle을 넘겨줌

activity?.supportFragmentManager!!.beginTransaction()
                        .replace(R.id.view_main, fragment2)
                        .commit()
```
```kotlin
// Fragment2에서 데이터 받기
val num1 = arguments?.getInt("num1")
val num2 = arguments?.getInt("num2")
```

- 위의 과정을 정리해보면 Activity/Fragment -> Fragment의 경우 Bundle을 사용하여서 아래와 같은 흐름대로 전달할 수 있고
```markdown
1) 이동할 Fragment 객체 생성
2) Bundle 객체 생성 및 데이터 저장 
→ bundle.putXXXX(name, value)
3) Fragment객체.arguments = Bundle객체
```

- Activity/Fragment -> Activity의 경우 Intent를 활용해서 아래와 같이 쓸 수 있음
```markdown
1) 이동할 액티비티 클래스를 담고 있는 Intent 객체 생성
2) Intent객체.putXXXExtra(name, value)
3) startActivity(Intent객체)
```

- 그리고 Fragment와 Activity에 따라서 startActivity가 다름, 이것은 Fragment 자체가 Activity와 같은 범주로써 활용되지 않기 때문에 호스팅 되는 Activity를 그리고 그 context를 활용해야함
```markdown
activity인 경우 :  startActivity(intent)
fragment인 경우인 경우 : activity.startActivity(intent)
```

- 이렇게 통신하는 방법에 관해서는 이전부터 자주 사용한 방법이긴 했으나 최근 startActivityForResult가 deprecated가 되면서 구글 권장사항은 Result API와 ViewModel을 활용하는 것임

## ViewModel을 사용하여 데이터 공유
- 앞서 다뤘던 부분은 이전부터 사용한 어떻게 보면 전형적으로 Intent와 Bundle등 일반적으로 쉽게 사용할 수 있는 범주에서의 내용이었고 이제부터 나오는 내용은 구글 공식문서 상에서 권장사항임(startActivityForResult 역시 Deprecated 됐음)

- ViewModel 자체가 UI 데이터를 저장하고 관리하기 때문에 이 부분을 활용함

- 아래와 같이 먼저 ViewModel을 만듬, LiveData를 통해서 값을 가지고 있음
```kotlin
class ItemViewModel : ViewModel() {
    private val mutableSelectedItem = MutableLiveData<Item>()
    val selectedItem: LiveData<Item> get() = mutableSelectedItem

    fun selectItem(item: Item) {
        mutableSelectedItem.value = item
    }
}
```

- 이렇게 처리한다면 Fragment와 Activity에서 모두 ViewModel을 공유 인스턴스를 검색할 수 있음, ViewModel을 인스턴스화 하여서 이 데이터를 관찰하고 수정할 수 있음
```kotlin
// Activity의 경우
class MainActivity : AppCompatActivity() {
    // Using the viewModels() Kotlin property delegate from the activity-ktx
    // artifact to retrieve the ViewModel in the activity scope
    private val viewModel: ItemViewModel by viewModels()
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        viewModel.selectedItem.observe(this, Observer { item ->
            // Perform an action with the latest item data
        })
    }
}
```
```kotlin
// Fragment의 경우
class ListFragment : Fragment() {
    // Using the activityViewModels() Kotlin property delegate from the
    // fragment-ktx artifact to retrieve the ViewModel in the activity scope
    private val viewModel: ItemViewModel by activityViewModels()

    // Called when the item is clicked
    fun onItemClicked(item: Item) {
        // Set a new item
        viewModel.selectItem(item)
    }
}
```

- Fragment 간 데이터 공유를 해야하는 경우도 있음, Fragment가 직접 통신하지 않고서는 구현하기 힘들고 직접 통신하는 것은 더 이상 독립적이지 않다는 것을 의미함

- 그래서 이러한 통신을 처리하기 위해서 ViewModel을 활용할 수 있음

- ViewModel을 먼저 만든 다음, Fragment가 동일한 범위를 사용하므로 동일한 ViewModel 인스턴스를 수신하여서 서로 통신이 가능함
```kotlin
// ViewModel 정의
class ListViewModel : ViewModel() {
    val filters = MutableLiveData<Set<Filter>>()

    private val originalList: LiveData<List<Item>>() = ...
    val filteredList: LiveData<List<Item>> = ...

    fun addFilter(filter: Filter) { ... }

    fun removeFilter(filter: Filter) { ... }
}
```

- 아래와 같이 서로 다른 Fragment여도 통신과 업데이트에 있어서 ListViewModel을 활용하여 처리할 수 있음
```kotlin
class ListFragment : Fragment() {
    // Using the activityViewModels() Kotlin property delegate from the
    // fragment-ktx artifact to retrieve the ViewModel in the activity scope
    private val viewModel: ListViewModel by activityViewModels()
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        viewModel.filteredList.observe(viewLifecycleOwner, Observer { list ->
            // Update the list UI
        }
    }
}
```
```kotlin
class FilterFragment : Fragment() {
    private val viewModel: ListViewModel by activityViewModels()
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        viewModel.filters.observe(viewLifecycleOwner, Observer { set ->
            // Update the selected filters UI
        }
    }

    fun onFilterSelected(filter: Filter) = viewModel.addFilter(filter)

    fun onFilterDeselected(filter: Filter) = viewModel.removeFilter(filter)
}
```

## Fragment Result API를 사용하여 결과 가져오기
- 일회성 값을 전달해야 하는 경우 Fragment Result API를 활용할 수 있음, 이를 통해서 서로 직접 참조하지 않아도 Fragment 결과를 설정하고 이러한 결과를 수신 대기하여 구성요소가 서로 통신할 수 있음

- 이러한 관리를 FragmentManager를 통해서 활용할 수 있음, Fragment 결과의 중앙 저장소 역할을 할 수 있음

- 만약 Fragment B에서 Fragment A로 데이터를 다시 전달하려면 Fragment A에서 리스너를 아래와 같이 설정함, 이는 setFragmentResultListener를 통해서 가능함

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    // Use the Kotlin extension in the fragment-ktx artifact
    setFragmentResultListener("requestKey") { requestKey, bundle ->
        // We use a String here, but any type that can be put in a Bundle is supported
        val result = bundle.getString("bundleKey")
        // Do something with the result
    }
}
```

- 결과를 생성하는 Fragment B에서 동일한 requestKey를 사용하여서 FragmentManager에 결과를 설정할 수 있음, 이는 setFragmentResult를 통해서 가능함
```kotlin
button.setOnClickListener {
    val result = "result"
    // Use the Kotlin extension in the fragment-ktx artifact
    setFragmentResult("requestKey", bundleOf("bundleKey" to result))
}
```

- 이를 도식화 시킨다면 아래와 같은 구조로 위의 작업이 이루어지는 것임
![one](/cheewr85/img/android/nine.png)

- 여기서 주어진 키에는 단일 리스너와 결과만 있을 수 있음, 리스너가 결과를 수신하고 onFragmentResult 콜백을 실행하면 결과는 삭제됨

- 이를 상위-하위 관계도 비슷하게 처리할 수 있음

- Activity의 경우 getSupportFragmentManager를 사용하여서 결과 리스너를 설정해서 받아올 수 있음
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        supportFragmentManager
                .setFragmentResultListener("requestKey", this) { requestKey, bundle ->
            // We use a String here, but any type that can be put in a Bundle is supported
            val result = bundle.getString("bundleKey")
            // Do something with the result
        }
    }
}
```