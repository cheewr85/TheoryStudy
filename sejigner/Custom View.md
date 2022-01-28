# Custom View

Android 기본 레이아웃 클래스 하 View, ViewGroup을 통해 미리 빌드된 위젯과 레이아웃을 제공

- 기본 위젯 : Button, TextView, EditText, ListView, CheckBox, RadioButton 등

- 기본 레이아웃 : LinearLayout, FrameLayout, RelativeLayout 등

기본 컴포넌트에 수정이 필요하면 View 서브클래스를 만들고 메서드를 오버라이드함



안드로이드 View 클래스는 다음과 같은 이유로 커스텀이 필요할 수 있음

- 더 나은 UI 디자인과 애니메이션

- 다른 방식의 유저 상호작용

- 다른 종류의 데이터 타입 보여주기

- 성능 최적화

- 재사용성

--- 



안드로이드는 기본 위젯을 갖고, 그 위젯들의 base 클래스는 뷰 클래스

![Basic Widget Hierarchy](https://koenig-media.raywenderlich.com/uploads/2017/11/Basic-Android-Widget-Hierarchy-1-650x332.png)

- View 
  스크린의 한 부분을 차지하고 그 자신과 자식(ViewGroup인 경우)을 그림
  하나의 뷰는 유저 이벤트를 담당

- ViewGroup
  뷰 클래스의 서브 클래스로 안드로이드 레이아웃의 base 클래스
  레이아웃 속성을 정의하고 각각의 서브뷰가 어디에 다른 것을 그릴지 결정

### 안드로이드 뷰가 그려지는 방식

액티비티가 포어그라운드로 올라오면 레이아웃 계층의 최상단인 루트 뷰에 요청
안드로이드는 최상단 부모부터 시작해서 자식으로 내려가면서 뷰를 그림
자식이 ViewGroup일경우 ViewGroup의 자식들을 그리고나서 다음 자식 뷰를 그림(depth-first traversal)
ViewGroup의 자식들은 xml 파일 위에서부터 아래 순서로 그려짐

순서

- Measure
  각각의 뷰가 자신의 사이즈를 측정

- Layout
  각각의 ViewGroup이 자식들의 사이즈와 레이아웃 규칙을 고려하여 자식들의 스크린 상 자리를 찾아줌

- Draw
  측정과 배치가 끝나면 각각의 뷰가 그려짐

### 

## Custom View 만들기 예제

```xml
<kr.co.selphone.welcome.login.CustomLoginButton
android:id="@+id/login_kakao"
style="@style/LoginButtonStyle"
app:bg="@drawable/login_kakao_bg"
app:symbol="@drawable/login_kakao_symbol"
app:text="카카오톡"
app:textColor="@color/kakao_brown"

/>


출처: https://gun0912.tistory.com/38 [박상권의 삽질블로그]
```

위 xml 코드는 매우 단순해보이는데, 이미 커스텀뷰를 정의할 때 필요한 레이아웃 속성들을 정의해주었기 때문

```xml
<kr.co.selphone.welcome.login.CustomLoginButton
android:id="@+id/login_kakao"
style="@style/LoginButtonStyle"
app:bg="@drawable/login_kakao_bg"
app:symbol="@drawable/login_kakao_symbol"
app:text="카카오톡"
app:textColor="@color/kakao_brown"
/>

<kr.co.selphone.welcome.login.CustomLoginButton
android:id="@+id/login_naver"
style="@style/LoginButtonStyle"
app:bg="@drawable/login_naver_bg"
app:symbol="@drawable/login_naver_symbol"
app:text="네이버"
app:textColor="@color/white"
/>

<kr.co.selphone.welcome.login.CustomLoginButton
android:id="@+id/login_google"
style="@style/LoginButtonStyle"
app:bg="@drawable/login_google_bg"
app:symbol="@drawable/login_google_symbol"
app:text="구글"
app:textColor="@color/google_red"
/>

출처: https://gun0912.tistory.com/38 [박상권의 삽질블로그]
```

이렇게 커스텀뷰를 사용하여 간단한 수정만으로 재사용 가능
복잡한 레이아웃 구성 역시 CustomView로 묶어두면 쉽게 사용 가능

### Custom View의 Layout XML 생성

```xml
<?xml version="1.0" encoding="utf-8"?> 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
android:id="@+id/bg" 
android:layout_width="match_parent" 
android:layout_height="55dp" 
android:background="@drawable/login_kakao_bg" 
android:gravity="center" 
android:padding="10dp"> 

<!-- android:background="@drawable/kakao_login_button_background" --> 

<ImageView android:id="@+id/symbol" 
android:layout_width="wrap_content" 
android:layout_height="32dp" 
android:layout_gravity="center_vertical" 
android:layout_marginLeft="20dp" 
android:src="@drawable/login_kakao_symbol" /> 

<TextView android:id="@+id/text" 
style="@style/CustomText_Subhead" 
android:layout_width="0dp" 
android:layout_height="match_parent" 
android:layout_weight="1" 
android:gravity="center" 
android:text="카카오톡 로그인" 
android:textColor="@color/kakao_brown" 
android:textStyle="bold" /> 

</LinearLayout>

출처: https://gun0912.tistory.com/38 [박상권의 삽질블로그]
```

### Attrs.xml 설정

value -> attrs.xml

attr 속성을 작성해 주어야 레이아웃에서 bg 태그로 작성된 속성과 View class 파일 속성이 연결됨

```xml
<declare-styleable name="LoginButton">
    <attr name="bg" format="reference|integer" />
    <attr name="symbol" format="reference|integer" />
    <attr name="text" format="reference|string" />
    <attr name="textColor" format="reference|integer" />
</declare-styleable>


출처: https://gun0912.tistory.com/38 [박상권의 삽질블로그]
```

### Custom View 작성

```kotlin

public class CustomLoginButton extends LinearLayout {

    LinearLayout bg;
    ImageView symbol;
    TextView text;

    public CustomLoginButton(Context context) {

        super(context);
        initView();

    }

    public CustomLoginButton(Context context, AttributeSet attrs) {

        super(context, attrs);

        initView();
        getAttrs(attrs);

    }

    public CustomLoginButton(Context context, AttributeSet attrs, int defStyle) {

        super(context, attrs);
        initView();
        getAttrs(attrs, defStyle);

    }

                        
    private void initView() {

        String infService = Context.LAYOUT_INFLATER_SERVICE;
        LayoutInflater li = (LayoutInflater) getContext().getSystemService(infService);
        View v = li.inflate(R.layout.welcome_login_button, this, false);
        addView(v);

        bg = (LinearLayout) findViewById(R.id.bg);
        symbol = (ImageView) findViewById(R.id.symbol);

        text = (TextView) findViewById(R.id.text);

    }

    private void getAttrs(AttributeSet attrs) {
        TypedArray typedArray = getContext().obtainStyledAttributes(attrs, R.styleable.LoginButton);

        setTypeArray(typedArray);
    }


    private void getAttrs(AttributeSet attrs, int defStyle) {
        TypedArray typedArray = getContext().obtainStyledAttributes(attrs, R.styleable.LoginButton, defStyle, 0);
        setTypeArray(typedArray);

    }


    private void setTypeArray(TypedArray typedArray) {


        int bg_resID = typedArray.getResourceId(R.styleable.LoginButton_bg, R.drawable.login_naver_bg);
        bg.setBackgroundResource(bg_resID);

        int symbol_resID = typedArray.getResourceId(R.styleable.LoginButton_symbol, R.drawable.login_naver_symbol);
        symbol.setImageResource(symbol_resID);

        int textColor = typedArray.getColor(R.styleable.LoginButton_textColor, 0);
        text.setTextColor(textColor);

        String text_string = typedArray.getString(R.styleable.LoginButton_text);
        text.setText(text_string);


        typedArray.recycle();

    }


    void setBg(int bg_resID) {

        bg.setBackgroundResource(bg_resID);
    }

    void setSymbol(int symbol_resID) {
        symbol.setImageResource(symbol_resID);
    }

    void setTextColor(int color) {

        text.setTextColor(color);
    }

    void setText(String text_string) {
        text.setText(text_string);
    }

    void setText(int text_resID) {
        text.setText(text_resID);
    }
}
```

![](https://t1.daumcdn.net/cfile/tistory/272C2635563743330E)
