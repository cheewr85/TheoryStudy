# Intent

다른 앱 컴포넌트로부터 작업을 요청할 때 사용되는 메시징 객체이다.

사용 사례는 다음과 같다.

- 액티비티 시작
  Intent로 시작할 액티비티를 설명하고 전달할 데이터를 담아서 startActivity() 메서드를 통해 전달한다.
  액티비티가 시작했다는 결과를 받으려면, startActivityForResult()를 호출하고, onActivityResult() 콜백이 Intent 객체를 수신한다\.

- 서비스 시작
   JobScheduler(API 21 이상) 또는 Service 클래스의 메서드(API 21 이하)를 사용하여 서비스를 시작한다.
  서비스를 시작하면서 데이터를 전달하려면 Intent 객체를 서비스에 전달한다.

- 브로드캐스트 전달
  모든 앱이 수신할 수 있는 메시지인 브로드캐스트는 시스템이 부팅될 때나 기기가 충전을 시작할 때 등 시스템 이벤트에 대한 정보를 담고 있는데,
  Intent가 발생한 브로드캐스트를 sendBroadcast()와 sendOrderedBroadcast()에 전달하여 다른 앱이 신호에 따라 처리하도록 한다.

### Intent 유형

- 명시적 인텐트
  인텐트를 충족하는 앱이 무엇인지 **명시**한다. 즉, 대상 앱의 패키지 이름과 컴포넌트 클래스명을 포함한다.
  보통 앱 내 특정 컴포넌트를 시작하기 위해 사용한다.

- 암시적 인텐트
  수행할 일반적인 작업을 선언하여 그 작업을 수행할 수 있는 다른 어떤 앱이든 사용할 수 있도록 한다.
  다른 앱이 인텐트를 인식하여 해당 작업을 수행하려면 인텐트 필터가 있어야 한다. 같은 인텐트 필터를 갖춘 앱이 여러가지일 경우, 다이얼로그로 사용자가 선택하게 한다.
  
  - **인텐트 필터**  매니페스트 파일에 들어있는 표현으로 앱 내 컴포넌트가 받게 될 인텐트의 유형을 나타낸다. 인텐트 필터가 없는 다른 앱 컴포넌트는 명시적 인텐트를 통해서만 시작할 수 있다.
  
  ![intent_example](img\intent_example.png)

### 인텐트 빌드

인텐트에 포함되어야 할 기본 사항 목록

- Component Name
  어떤 컴포넌트를 시작할지 알려준다. 즉, 구성요소명을 통해 컴포넌트를 명시적인 것으로 만들어준다. 없으면 암시적 인텐트로 간주된다.
  Intent의 필드에 ComponentName 객체를 통해 참조할 수 있는데, 이때 com.example.ExampleActivity의 형식으로 앱의 패키지 이름이 포함된 완전히 정규화된 클래스 이름을 사용해야 한다.

- Action
  앱 내에 있는 인텐트가 사용할 작업을 직접 지정할 수도 있지만, 보통 Intent 클래스나 다른 프레임워크 클래스가 정의한 작업 상수를 지정한다.
  
  - ACTION_VIEW
    해당 컴포넌트가 표시할 수 있는 정보를 갖추고 있을 때 사용한다. ex) 갤러리 앱, 지도 앱
  
  - ACTION_SEND
    공유 인텐트라고도 불리며, 사용자가 다른 앱을 통해 공유할 수 있는 데이터를 가지고 있을 때 사용한다. ex) 이메일 앱, 소셜 공유 앱

- Data
  작업을 수행할 데이터 또는 데이터의 MIME 유형을 참조하는 URI 객체이다. 둘 다 참조하거나, 둘 중 하나만 참조할 수 있다.

- Category
  인텐트를 처리할 컴포넌트의 종류에 관한 추가 정보를 담은 문자열이다. 잘 사용되지는 않는다.
  
  - CATEGORY_BROWSABLE
    대상 액티비티가 웹브라우저에서 시작되도록 하고 참조된 데이터를 표시하도록 한다.
  
  - CATEGORY_LAUNCHER
    이 액티비티가 최초로 사용되어야 함을 나타낸다. 앱 프로젝트를 생성하면 기본적으로 MainActivity에 해당 카테고리가 포함되어 있다.

- Extras
  인텐트를 통해 추가적으로 전달할 데이터가 담긴 Key-Value 쌍이다. 몇 개의 단일 쌍일 경우에는 putExtra를 통해 데이터를 담지만, 여러개의 데이터를 한번에 전달할 때는 Bundle 객체를 만들어서 putExtra를 통해 삽입한다. Intent 클래스는 표준화된 데이터 유형을 EXTRA_ * 형식의 상수로 제공한다.

- Flags
  안드로이드 시스템에 액티비티 시작 방식에 대해 지침을 줄 수도 있고, 액티비티를 시작한 후 인텐트를 어떻게 처리할지 알려주는 등 인텐트에 대한 메타데이터 기능을 한다.

### 인텐트 필터 예시

```xml
<activity android:name="MainActivity" android:exported="true">
    <!-- This activity is the main entry, should appear in app launcher -->
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>

<activity android:name="ShareActivity" android:exported="false">
    <!-- This activity handles "SEND" actions with text data -->
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
    <!-- This activity also handles "SEND" and "SEND_MULTIPLE" with media data -->
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <action android:name="android.intent.action.SEND_MULTIPLE"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="application/vnd.google.panorama360+jpg"/>
        <data android:mimeType="image/*"/>
        <data android:mimeType="video/*"/>
    </intent-filter>
</activity>
```

### 보류 인텐트

외부 앱에 권한을 허가하여 외부 앱이 인텐트를 통해 사용할 수 있도록 한다. 말 그대로 인텐트 통신이 **보류**된 상태로, 외부 앱에서 인텐트를 통해 마치 앱 내부 프로세스인 듯 접근할 수 있도록 한다.

- 예시
  
  - Android 시스템이 알람 시간이 되어 NotificationManager Intent를 생성하면 특정 앱이 받아서 작업을 실행한다.
  
  - 앱 위젯에서 인텐트를 받아 실행되도록 한다.
  
  - AlarmManager에서 생성된 Intent를 통해 지정된 시간에 특정한 작업이 실행되도록 한다.

- 사용 방법
  
  - Activity를 시작하는 Intent : PendingIntent.getActivity()
  
  - Service를 시작하는 Intent : PendingIntent.getService()
  
  - BroadcastReceiver를 시작하는 Intent : PendingIntent.getBroadcast()
