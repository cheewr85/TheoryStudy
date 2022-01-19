# Data Binding

- Android Jetpack 라이브러리

- 동적 방식이 아닌 선언적 형식으로 레이아웃의 UI 컴포넌트들을 앱 데이터 소스와 결합

- Data와 View를 연결하는 작업을 레이아웃에서 처리

```kotlin
findViewById<TextView>(R.id.sample_text).apply {
        text = viewModel.userName
    }
```

기존 findViewById()를 호출하여 TextView 위젯을 찾아서 viewModel 변수의 userName 속성에 결합하는 방식

```kotlin
<TextView
        android:text="@{viewmodel.userName}" />
```

데이터 바인딩 방식으로, 레이아웃 파일에서 뷰모델의 변수를 할당

- Activity에서 UI 프레임워크 호출 삭제 -> 파일 단순화, 유지보수 용이

- 앱 성능 향상, 메모리 누수, NPE 방지

## 레이아웃 및 결합 표현식

- 표현식 언어로 레이아웃의 뷰와 변수를 연결

- UI 레이아웃 루트 요소의 동위 요소인 data 요소 내에 정의 -> layout 태그로 래핑

```kotlin
<layout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto">
        <data>
            <variable
                name="viewmodel"
                type="com.myapp.data.ViewModel" />
        </data>
        <ConstraintLayout... /> <!-- UI layout's root element -->
    </layout>
```

### 식별 가능한 데이터 객체 작업

- 기본 데이터 소스가 변경될 때 UI를 새로고침 할 필요X

- 변수와 속성, 객체, 필드, 컬렉션을 식별 가능하도록 함

### 바인딩 클래스 생성

- 라이브러리가 변수와 뷰에 액세스하는 결합 클래스 생성 

### 결합 어댑터

- 속성 또는 리스너를 설정하는 데 필요한 프레임워크를 호출

- setText() 메서드를 호출하여 텍스트 속성을 설정

- setOnClickListener() 메서드를 호출하여 리스너를 클릭 이벤트에 추가

```kotlin
 @BindingAdapter("app:goneUnless")
    fun goneUnless(view: View, visible: Boolean) {
        view.visibility = if (visible) View.VISIBLE else View.GONE
    }
```

### DataBinding vs ViewBinding

![](https://blog.kakaocdn.net/dn/lYcJ4/btqZkqGoZ14/QjlZ7JvUXhxdXsfO0BNC1K/img.jpg)

- DataBinding
  
  - 레이아웃 파일에서 <layout> 태그 사용
  
  - 루트 뷰에 Tag 삽입

- ViewBinding
  
  - 레이아웃 변수와 레이아웃 표현식 지원 X -> Xml 데이터와 레이아웃의 바인딩 사용 불가
  
  - 루트뷰에 Tag 삽입 X
  
  - 어노테이션을 활용하여 데이터바인딩보다 더 빠르게 바인딩 클래스 생성

> View와 Model을 연결하기 위해 DataBinding이 개발되었는데 개발자들이 단순히 View에 대한 참조를 얻기 위한 목적으로 DataBinding을 활용하는 사례가 많아 ViewBinding이 개발됨

![](https://blog.kakaocdn.net/dn/dy4YeK/btqRt54jsio/kLFT8YPFuVbDzeJQnV8940/img.png)

**??? = ViewBinding**

- 편히 사용하던 Kotlin Synthetic이 안드로이드 4.1 기준으로 Deprecated

- 2019년부터 ViewBinding이 대체

- Synthetic은 코드 작성시에는 편하지만 오류가 생길 경우 빌드 타임이 아닌 런타임

- ViewHolder에서는 캐싱이 이루어지지 않아 RecyclerView에서 문제 발생

> ViewBinding, DataBinding, ViewModel, LiveData 모두 밀접한 관계가 있어서 따로 공부하는 것 보다는 직접 조합하여 써보는 것이 학습에 효과적
> 
> DataBinding, ViewBinding이 단독으로 쓰이는 것은 학습 단계 정도 일 것. ViewModel과 LiveData 활용이 기본
