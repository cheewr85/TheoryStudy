# Fragment

- 자체 레이아웃을 정의 및 관리하고 자체 수명 주기를 보유하며 자체 입력 이벤트를 처리할 수 있음

- Activity나 다른 프래그먼트에서 호스팅되어야 함

- 단일 화면이나 화면 일부의 UI를 정의하고 관리하는데 적합함

- UI를 프래그먼트로 나눌 시 런타임 시 Activity의 모양을 더 쉽게 수정할 수 있음


### Fragment 사용 및 관리
- Fragment 클래스를 상속 받아서 그 안에 있는 메소드를 오버라이딩 하여서 사용할 수 있음

- XML 상에서 Activity에서 Fragment를 추가할 수 있고 혹은 Activity 상에서 내부적인 코드로 처리해서 추가하여서 사용할 수 있음

- 이러한 관리를 위해서 FragmentManager를 통해서 작업을 추가, 삭제 또는 교체하거나 백 스택에 추가하는 등의 작업을 할 수 있음

- 이 FragmentManager를 통해서 Activity에서 getSupportFragmentManager를 통해서 액세스 할 수 있고 Fragment 상에서 역시 getChildFragmentManager 혹은 getParentFragmentManager를 통해서 액세스 할 수 있음

- 예시로 아래와 같은 구조로써 FragmentManager로 연결하고 관리할 수 있음

![one](/cheewr85/img/android/six.png)

- 이렇게 FragmentManager를 사용한다면 변경사항이나 Fragment를 사용하는 모든 상황이 FragmentTransaction이라는 단일 단위로 커밋이 되서 관리가 됨

- 이러한 Transaction을 통해서 컨테이너에 add 또는 replace 하는 작업을 할 수 있음 즉, 레이아웃 컨테이너에 있는 Fragment를 화면으로써 사용한다면 이와 같이 활용할 수 있음을 의미함

- Transaction 관리를 FragmentManager에서 한다고 하였는데, 이는 즉 런타임시 유저의 상호작용에 따라서 Fragment를 add, remove, replace 등의 작용을 할 수 있음을 의미함, 이런식의 과정과 변화를 transaction의 단위로 커밋을 하면서 반영을 하는 것을 의미함

- 이 의미는 앞서 말했듯이 단일 화면이나 화면 일부의 UI를 정의하고 관리하는데 있어서 Activity로 모두 다 처리하는 것이 아닌 Fragment로 관리함으로써 좀 더 쉽게 수정하고 관리를 할 수 있는 것임

### Fragment 수명 주기
- Fragment도 자체적인 수명 주기를 가지고 있는데 아래와 같은 수명 주기를 가짐

![one](/cheewr85/img/android/seven.png)

- Fragment는 크게 INITIALIZED, CREATED, STARTED, RESUMED, DESTROYED의 상태를 가지고 onCreate, onStart, onResume, onPause, onStop, onDestroy의 콜백을 가지고 있음

- Fragment가 인스턴스로 생성되면 처음엔 INITIALIZED 상태가 되고 여기서 반드시 FragmentManager가 추가를 해줘야함, 그리고 FragmentManager는 Fragment 상태변화에 있어서 그 상태를 관리하고 결정해야하는 책임이 있음 

- 그리고 추가적으로 Activity에 붙거나 Fragment가 사용되지 않아서 해제할 때 Fragment는 onAttach라는 콜백과 onDetach라는 콜백이 반드시 일어나게 됨

- onAttach를 통해서 호스트 하는 Activity에 붙게끔 FragmentManager가 추가를 해주고 활성화 되면서 수명 주기를 관리함, 이 콜백은 수명주기 상태가 바뀌기 전에 항상 불려짐

- onDetach의 경우, 호스트 하는 Activity에서 제거될 때 불러일으켜 지고 수명주기 상태가 바뀐 후에 항상 불려짐

- 여기서 이 Attach와 Detach는 FragmentTransaction의 attach, detach와는 무관함

- CREATED 된 상태는 이미 FragmentManager를 통해서 추가가 되고 onAttach 콜백이 불려진 상태이고, 이는 Fragment가 어디에 상태가 저장되어야 하는지 정해진 상태이고 이로 인해 onCreate가 불려지고 마치 Activity에서 처럼 onCreate 되듯이 Fragement 역시 생성이 됨

- 그리고 Fragment가 CREATED 되고 View가 INITIALIZED 됨으로써 Fragment의 View를 업데이트해서 불러올 수 있음, 이때 onViewCreated도 콜백이 불려짐, 그러면서 이 떄 View의 초기 상태를 설정할 수 있음

- 이런 과정 이후에 Fragment와 View 모두 CREATED 상태가 됨, 그리고 Fragment와 View가 STARTED 상태로 넘어감, 이때는 Fragment의 View들이 이용 가능해지고 FragmentTransaction 등 활용하여서 이런 상태가 보장이 됨 이 때 onStart가 콜백됨

- 그리고 애니메이션이나 Transition이 Fragment가 보여져서 다 끝났으므로 사용자와 상호작용을 위해서 RESUMED 상태로 넘어감

- 그 이후 사용자가 Fragment를 떠났지만 아직 Fragment가 보이는 경우, STARTED 상태이면서 onPause가 콜백이 된 상태임

- 그리고 더 이상 Fragment가 더 이상 보이지 않는 경우 CREATED 상태가 되면서 onStop이 콜백이 되면서 상태에 대한 전환이 일어남, 여기서 상태의 저장과 전환에 대해서 아래와 같이 API 버전에 따라 상이하게 나타남

![one](/cheewr85/img/android/eight.png)

- 모든 작업이 완료되면 Fragment는 CREATED 상태 View는 DESTROYED 상태로 넘어가고 모든 Fragment View들은 제거가 됨, 그리고 완전히 제거 됐을 때 Fragment 역시 DESTROYED 상태로 넘어가게 됨

### Fragment 상태 저장
- Fragment 역시 수명 주기를 가지고 있기 때문에 데이터에 대해서 자동으로 백 스택에 저장하고 복원을 함, 이를 확인을 할 수 있음

- 이 작업은 Activity에서 onSaveInstanceState를 통해서 데이터를 Bundle 값으로 저장하고 복원할 수 있는 형태가 동일함

#### 예시
- 아래의 예시는 NavigationView의 버튼을 누르면 Main에서 컨테이너 역할을 하는 FrameLayout을 통해서 각각 생성한 Fragment로 바뀌는 화면임

- 우선 Fragment의 View의 경우 일반적으로 Activity와 같이 View를 만들고 처리했음, 여기서는 인자로 레이아웃 파일을 넘겨서 알아서 Fragment로 attach 됨이 다르고 나머지는 수명 주기를 고려하여서 View가 CREATED 상태가 되기 전 마치 Activity에서 onCreate 하듯이 View에 대한 정보를 불러와서 이벤트 처리를 함

- 그리고 onStart 상태에서 로그인 확인을 함 왜냐하면 만약 버튼을 눌러서 다른 Fragment로 갔다가 다시 돌아왔는데 이미 로그인을 했는데 또 로그인을 해야하는 상황을 방지하기 위해서 수명주기 상태에 맞게 적절하게 코드 처리를 함
```kotlin
package techtown.org.retailmarket.mypage

import android.os.Bundle
import android.view.View
import android.widget.Toast
import androidx.core.widget.addTextChangedListener
import androidx.fragment.app.Fragment
import com.google.firebase.auth.FirebaseAuth
import com.google.firebase.auth.ktx.auth
import com.google.firebase.ktx.Firebase
import techtown.org.retailmarket.R
import techtown.org.retailmarket.databinding.FragmentMypageBinding

// 인자로 레이아웃 파일을 넘기면 자동으로 attach됨
class MyPageFragment: Fragment(R.layout.fragment_mypage) {

    private var binding: FragmentMypageBinding? = null
    private val auth: FirebaseAuth by lazy {
        Firebase.auth
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        // View Binding 활용함
        val fragmentMypageBinding = FragmentMypageBinding.bind(view)
        binding = fragmentMypageBinding

        // 각 버튼대로 클릭 리스너 처리를 함
        fragmentMypageBinding.signInOutButton.setOnClickListener {
            binding?.let { binding ->
                val email = binding.emailEditText.text.toString()
                val password = binding.passwordEditText.text.toString()

                // 현재 로그인이 되어 있다면 로그아웃, 로그인이 되어있지 않다면 로그인 시켜주면 됨
                if (auth.currentUser == null) {
                    // 로그인 해야함
                    auth.signInWithEmailAndPassword(email, password)
                        .addOnCompleteListener(requireActivity()) { task ->
                            if (task.isSuccessful) {
                                successSignIn()
                            } else {
                                Toast.makeText(context, "로그인에 실패했습니다. 이메일 또는 비밀번호를 확인해주세요.", Toast.LENGTH_SHORT).show()
                            }
                        }
                } else {
                    // 로그아웃 해야함
                    auth.signOut()
                    // 로그아웃 하면서 EditText 초기화하고 버튼도 바꿈
                    binding.emailEditText.text.clear()
                    binding.emailEditText.isEnabled = true
                    binding.passwordEditText.text.clear()
                    binding.passwordEditText.isEnabled = true

                    binding.signInOutButton.text = "로그인"
                    binding.signInOutButton.isEnabled = false
                    binding.signUpButton.isEnabled = false
                }
            }
        }

        fragmentMypageBinding.signUpButton.setOnClickListener {
            // 회원가입 하는 경우
            binding?.let { binding ->
                val email = binding.emailEditText.text.toString()
                val password = binding.passwordEditText.text.toString()

                auth.createUserWithEmailAndPassword(email, password)
                    .addOnCompleteListener(requireActivity()) { task->
                        if(task.isSuccessful) {
                            Toast.makeText(context, "회원가입에 성공했습니다. 로그인 버튼을 눌러주세요.", Toast.LENGTH_SHORT).show()
                        } else {
                            Toast.makeText(context, "회원가입에 실패했습니다. 이미 가입한 이메일일 수 있습니다.", Toast.LENGTH_SHORT).show()
                        }
                    }
            }
        }

        // enable을 조절하기 위해서 editText의 리스너처리함
        fragmentMypageBinding.emailEditText.addTextChangedListener {
            binding?.let { binding ->
                val enable = binding.emailEditText.text.isNotEmpty() && binding.passwordEditText.text.isNotEmpty()
                binding.signInOutButton.isEnabled = enable
                binding.signUpButton.isEnabled = enable
            }
        }

        fragmentMypageBinding.passwordEditText. addTextChangedListener {
            binding?.let { binding ->
                val enable = binding.emailEditText.text.isNotEmpty() && binding.passwordEditText.text.isNotEmpty()
                binding.signInOutButton.isEnabled = enable
                binding.signUpButton.isEnabled = enable
            }
        }


    }

    override fun onStart() {
        super.onStart()
        // 로그인 풀렸는지 확인
        if(auth.currentUser == null) {
            // 로그인이 안된 상태는 로그아웃 된 상태와 동일함
            binding?.let { binding ->
                binding.emailEditText.text.clear()
                binding.emailEditText.isEnabled = true
                binding.passwordEditText.text.clear()
                binding.passwordEditText.isEnabled = true

                binding.signInOutButton.text = "로그인"
                binding.signInOutButton.isEnabled = false
                binding.signUpButton.isEnabled = false
            }
        } else {
            // 현재 유저가 있다면
            binding?.let { binding ->
                binding.emailEditText.setText(auth.currentUser!!.email)
                binding.emailEditText.isEnabled = false
                binding.passwordEditText.setText("*******")
                binding.passwordEditText.isEnabled = false

                binding.signInOutButton.text = "로그아웃"
                binding.signInOutButton.isEnabled = true
                binding.signUpButton.isEnabled = false
            }
        }
    }

    private fun successSignIn() {
        // 로그인 성공시 editText, 버튼을 잠궈주고 로그인을 로그아웃 버튼으로 교체해줌
        if (auth.currentUser == null) {
            // 로그인 실패시 예외처리
            Toast.makeText(context, "로그인에 실패했습니다. 다시 시도해주세요.",Toast.LENGTH_SHORT).show()
            return
        }

        binding?.emailEditText?.isEnabled = false
        binding?.passwordEditText?.isEnabled = false
        binding?.signUpButton?.isEnabled = false
        binding?.signInOutButton?.text = "로그아웃"
    }
}
````

- 그리고 다른 Fragment 역시 위와 같이 View를 생성하였고 이제 Main에서는 FrameLayout으로 Fragment를 다르게 했기 때문에 이 상황을 버튼을 누르면 바꾸게끔 해주는데 이때 FragmentManager를 활용하고 버튼을 눌렀을 때마다 UI가 바뀌게 Transaction 처리를 해 줌

```kotlin
package techtown.org.retailmarket

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.fragment.app.Fragment
import com.google.android.material.bottomnavigation.BottomNavigationView
import techtown.org.retailmarket.chatlist.ChatListFragment
import techtown.org.retailmarket.home.HomeFragment
import techtown.org.retailmarket.mypage.MyPageFragment

class MainActivity : AppCompatActivity() {


    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 프래그먼트를 사용하기 위해서 생성을 함
        val homeFragment = HomeFragment()
        val chatListFragment = ChatListFragment()
        val myPageFragment = MyPageFragment()

        val bottomNavigationView = findViewById<BottomNavigationView>(R.id.bottomNavigationView)

        // 초기에는 아무것도 없으므로 초기 프래그먼트 설정함
        replaceFragment(homeFragment)

        // 메뉴 아이템을 선택해서 프래그먼트에 붙일 수 있게 처리함
        bottomNavigationView.setOnNavigationItemSelectedListener {
            // menu에서 선택한 item들을 활용할 수 있음
            when (it.itemId) {
                // 각각 생성한 프래그먼트를 매개변수로 넘겨서 교체해줌
                R.id.home -> replaceFragment(homeFragment)
                R.id.chatList -> replaceFragment(chatListFragment)
                R.id.myPage -> replaceFragment(myPageFragment)
            }
            true
        }
    }

    private fun replaceFragment(fragment: Fragment) {
        // 버튼 누른것에 따라서 프래그먼트를 바꾸기 위한 로직
        supportFragmentManager.beginTransaction()
            .apply {
                // fragment를 보여줄 FrameLayout에 각각의 fragment를 그리게끔 변경함
                replace(R.id.fragmentContainer, fragment)
                commit()
            }
    }
}
```