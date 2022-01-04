# Android 4 Basic Components

Activity - Service - BroadcastReceiver - ContentProvider

---

![](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/8f2050b1-f6aa-4fcf-9888-ba274a342c9c/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220104%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220104T062116Z&X-Amz-Expires=86400&X-Amz-Signature=868eaa04f62d83c93e02e6790efe445e55432134d9035516c42676c65ece6521&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

## Activity

- 유저와 상호작용하는 UI를 담당
- 안드로이드 애플리케이션은 반드시 하나 이상의 액티비티 포함
- 여러 액티비티 동시 Display 불가능 → Fragment+Activity 가능
- 하나 이상의 View 또는 ViewGroup 포함
- 앱의 상태에 따라 Activity Lifecycle이 변화하며, 그에 따라 Lifecycle Callback 호출

## Service

- Background 작업을 처리
- 유저와 직접적인 상호작용 X, 즉 UI 없음
- 네트워크와 연동
- 앱이 종료되어도 백그라운드에서 계속 동작
- 유저에게 보이지 않게 작동하지만, main thread(UI thread)에서 작동
- 사용자가 Service가 실행되고 있음을 인식할 수 있는 작업과 인식할 수 없는 작업으로 구분
  - 인식 가능 Service : 음악 재생은 앱의 Service가 백그라운드에서 작동하고 있기에 가능

```kotlin
class MyFirebaseMessagingService : FirebaseMessagingService() {

    private val ADMIN_CHANNEL_ID = "admin_channel"

    override fun onMessageReceived(remoteMessage: RemoteMessage) {
        super.onMessageReceived(remoteMessage)
        Log.d(TAG, "From: ${remoteMessage.from}")
        Log.d(TAG, "noti preference : ${prefs.getBoolean("notification", true)}")
        // Check if message contains a data payload.
        if (!remoteMessage.data.isNullOrEmpty()) {
            Log.d(TAG, "Message data payload: ${remoteMessage.data}")
            if(prefs.getBoolean("notification",true)) {
                sendNotification(remoteMessage)
            }
        }
    }
}
```

## BroadcastReceiver (방송수신자)

- 단말기의 안드로이드 OS에서 발생하는 이벤트와 정보를 받고 처리
- 화면의 꺼짐/ 켜짐, 리부팅, 네트워크 끊김, 배터리 부족, 문자 수신 등
- 안드로이드 OS가 Broadcast하면 그것을 앱의 BroadcastReceiver가 받아서 앱에 맞춰 대응
- OS뿐 아니라 앱 역시 다른 앱에 Broadcast 가능
- UI를 사용하지 않고, 상태 표시줄 알림이 일반적

## ContentProvider (콘텐트제공자)

- 공유가 허용된 앱 데이터를 관리
- 파일 입출력, SQLite 데이터베이스, 웹, 그 외 다양한 저장소에 저장된 데이터를 관리
- 해당 권한을 가진 다른 앱에 데이터를 제공
- Read & Write Permission을 가진 다른 앱이 Android System의 ContentProvider를 통해 연락처를 쿼리하거나 변경
- 데이터베이스의 CRUD(Create, Read, Update, Delete) 원칙 준수
- 작은 데이터를 Intent를 통하여 공유한다면, 큰 데이터는 ContentProvider를 통해 공유

## Intent

- Activity, Service, BroadcastReceiver는 비동기식 메시지 Intent로 활성화
- 일종의 메신저로서 컴포넌트를 연결하고, 데이터를 주고 받을 수 있도록 함
- Activity & Service : 인텐트를 통해 ‘어떤 작업을 할 것인가’에 대한 정보를 인텐트를 통해 넘겨 받음 (액티비티 실행, 액티비티에 이미지 표시, 웹 페이지 실행 요청 등)
- BroadcastReceiver : broadcast될 알림을 정의하는 목적으로 인텐트 이용
