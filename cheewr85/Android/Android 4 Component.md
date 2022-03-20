## 안드로이드 4대 컴포넌트
![one](/cheewr85/img/android/one.png)

- 위와 같이 Android 앱의 필수적인 기본 구성 요소가 존재하고 해당 구성 요소는 시스템이나 사용자가 앱에 들어올 수 있는 진입점이자 다른 구성 요소에 종속되는 구성 요소도 있음
- 각 유형은 뚜렷한 목적을 수행하고 각자 나름의 수명 주기가 있어 구서 요소의 생성 및 소멸 방식을 정의함

### Activity
- 사용자와 상호작용하기 위한 진입점, 사용자 인터페이스를 포함한 화면 하나를 나타냄 
- 이메일 앱을 예를 들면 새 이메일 목록을 표시하는 Activity가 하나 있고, 이메일을 작성하는 Activity가 또 하나 있고, 이메일을 읽는데 쓰는 Activity가 있을 수 있음, 이러한 여러 Activity가 함께 작동하여 이메일 앱을 구성함
- 이런 것들이 짜임새 있게 구성한 것도 맞지만 각자 서로 독립되어 있음, 그렇기 때문에 시스템과 앱의 주요 상호작용을 도움
- 사용자가 현재 관심을 가지고 있는 사항을 추적하여(화면에 표시된 것) Activity를 호스팅하는 프로세스를 시스템에서 실행하도록 함
- 이전에 사용한 프로세스에 사용자가 다시 찾을 만한 Activity가 있다는 것을 알고 해당 프로세스를 유지하는 데 더 높은 우선순위를 부여함
- 앱이 프로세스를 종료하도록 도와서 이전 상태가 복원되는 동시에 사용자가 Activity로 돌아갈 수 있게함
- 앱이 서로 사용자 플로우를 구현하고 시스템이 이러한 플로우를 조정하기 위한 수단을 제공함
![one](/cheewr85/img/android/two.png)

- 위와 같은 생명주기를 역시 가지고 있음

### 예시
- 이러한 Activity의 특성과 생명주기를 활용하여서 앱의 기능 이용시 활용할 수 있음
- 아래의 예시코드는 전자액자 앨범으로 일정시간 동안 저장된 사진을 보여주는 것인데 생명주기와 연관된 부분 코드를 본다면
```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_photo_frame)

        getPhotoUriFromIntent()

    }
```
- 먼저 onCreate 같은 경우, 해당 Activity에서 저장된 사진을 보여주는 것이므로 사진 데이터를 가져오는 함수가 onCreate에서 시작
- 그리고 Timer를 설정해서 일정시간 동안 돌아가면서 보여줌, 근데 이 Timer 자체가 쓰레드 작업을 통한 백그라운드 작업이므로 생명주기를 활용해서 Activity 상황에 맞게 설정을 해 줌
```kotlin
// 액티비티가 백그라운드로 들어가 더 이상 사용되지 않을 때
    override fun onStop() {
        super.onStop()

        timer?.cancel() // 타이머 종료시킴
    }

    override fun onStart() {
        super.onStart()
        // 다시 시작될 때는 타이머를 새롭게 시작하면 됨
        startTimer()
    }

    // 앱이 아예 완전히 꺼진것이므로 타이머를 꺼버려야함
    override fun onDestroy() {
        super.onDestroy()

        timer?.cancel()
    }
```
- 이런식으로 Activity 자체가 위에서 설명한대로 시스템에 실행하고 프로세스 상에서 우선순위 등 수명주기에 맞게 처리가 되기 때문에 이런식의 플로우와 조정 수단 그리고 상태를 표현하고 조절 가능함

### Content Provider
- 파일 시스템, SQLite 데이터베이스, 웹상이나 앱이 액세스 할 수 있는 다른 모든 영구 저장 위치에 저장 가능한 앱 데이터의 공유형 집합을 관리함
- 다른 앱은 Content Provider를 통해 해당 데이터를 쿼리하거나 허용할 경우 수정도 가능함
- 시스템은 Content Provider를 URI 구성표로 식별되고 이름이 지정된 데이터 항목을 게시할 목적으로 앱에 진입하기 위한 입구임
- URI를 통해 넣을 데이터를 매핑할 방식을 정하고 다른 곳에 전달하고 URI를 통해 데이터에 액세스 할 수 있음
- 시스템은 URI를 소유한 앱이 해당 URI에서 앱의 데이터를 검색할 때만 실행되도록 하면 됨
- URI를 통해서 이미지를 URI에 할당하고 Content Provider가 검색하도록 하여 다른 앱이 자유롭게 액세스 하지 못하게 막을 수 있고, 임시 권한을 부여해서 액세스를 허용할 수 있음

### 예시
- 위에서 설명했다시피 Content Provider를 활용해서 데이터에 액세스해서 데이터를 활용할 수 있다고 하였는데, 아래와 같이 앨범에 있는 저장된 이미지를 가져오기 위해서 Intent를 통해서 권한을 가져오고 처리함
```kotlin
private fun navigatePhotos() {
        // 권한이 정상적으로 잘 부여되었을 때 사진을 가져오게 함
        // SAF 기능을 사용함, 인텐트 활용해서
        val intent = Intent(Intent.ACTION_GET_CONTENT) // SAF 기능 사용 컨텐츠를 가져오게 함
        intent.type = "image/*" // 이미지만 가져올 것임
        startActivityForResult(intent, 2000) // 사진을 선택하고 돌아올 것이기 때문에 콜백으로 2000의 requestCode를 부여함
    }
```

- 그러면서 앞서 이러한 데이터를 URI를 통해서 맵핑해서 가져온다고 하였는데 이를 사전에 정의한 URI MutableList에 URI 형태를 담을 수 있음
```kotlin
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)

        if(resultCode != Activity.RESULT_OK) {
            return // 만일 정상적으로 결과를 받은게 아닌 경우가 있을 수 있으므로 return을 처리함
        }

        // 사진을 선택하고 받아올 때 2000의 requestCode로 받아옴
        when(requestCode) {
            2000 -> {
                // 이미지는 data(Intent)로 받음, Intent가 데이터를 null, 안 내려줬을 때 오류가 발생할 수 있으므로 nullable로 선언함, Uri도 똑같이함
                val selectedImageUri: Uri? = data?.data // 인텐트에 있는 데이터 가져옴

                if(selectedImageUri != null) {

                    // 6개 이상일 경우 토스트 메시지 띄움, 예외처리
                    if(imageUriList.size == 6) {
                        Toast.makeText(this, "이미 사진이 꽉 찼습니다.",Toast.LENGTH_SHORT).show()
                        return
                    }

                    imageUriList.add(selectedImageUri) /// 이미 널체크를 했으므로 정상적으로 추가가 됨, 위에서 선언한 리스트에서
                    imageViewList[imageUriList.size - 1].setImageURI(selectedImageUri) // 위에서 크기를 설정했고 그 다음 그 이미지를 그대로 넣어주면 됨
                } else {
                    // null을 받은 경우
                    Toast.makeText(this, "사진을 가져오지 못했습니다.",Toast.LENGTH_SHORT).show()
                }
            }
            else -> {
                // 다른 requestCode는 없으므로 예외처리만 함
                Toast.makeText(this, "사진을 가져오지 못했습니다.",Toast.LENGTH_SHORT).show()
            }
        }
    }
```

- 여기서 이와 관련된 권한처리는 확실하게 받아줘야함

### Broadcast Receiver
- Broadcast Receiver는 시스템이 장기적인 사용자 플로우 밖에서 이벤트를 앱에 전달하도록 지원하는 구성 요소로, 앱이 시스템 전체의 Broadcast 알림에 응답할 수 있게 함
- 앱으로 들어갈 수 있는 또 다른 명확한 진입점이기 때문에 시스템이 현재 실행되지 않은 앱에도 Broadcast를 전달할 수 있음
- 대다수의 Broadcast는 시스템에서 발생함, 화면이 꺼지거나 배터리가 부족하거나 사진을 캡처한 것 등
- 앱도 Broadcast를 사용할 수 있음, 기기에 다운로드되었고 이를 사용할 수 있다는 것을 알리거나 알림을 생성하여서 알릴 수 있음
- 이 Broadcast는 Intent 객체로 전달됨

### 예시
- 알람앱을 예시로 둘 수 있음, 알람이라는 이벤트에 대해서 BroadcastReciver를 통해서 채널을 만들어서 알람에 대한 정보를 알람 매니저를 등록하고 이를 인텐트 처리를 하여서 Broadcast를 통해서 설정을 할 수 있게 처리한 것임
```kotlin
package techtown.org.alarm

import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.os.Build
import androidx.core.app.NotificationCompat
import androidx.core.app.NotificationManagerCompat

// BroadcastReceiver를 상속받음, PendingIntent를 받아서 처리를 함
class AlarmReceiver: BroadcastReceiver() {
    companion object {
        // ID를 별도로 빼 둠
        const val NOTIFICATION_ID = 100
        const val NOTIFICATION_CHANNEL_ID = "1000"
    }

    // pendingintent를 받아서 처리하는 함수 -> Notification으로 처리할 것임
    override fun onReceive(context: Context, intent: Intent) {
        // 26버전 이상에서는 notification 처리를 위해 채널이 필요함
        createNotificationChannel(context)
        notifyNotification(context)
    }

    private fun createNotificationChannel(context: Context) {
        // MainActivity에서 this가 사용가능했음(Activity 자체를 context로 봐도 되니깐
        // 하지만 현재 클래스에서는 Activity가 아니므로 context를 따로 받아와야 함
        // context -> 앱의 글로벌 정보, API, 시스템에 대한 정보, SharedPreferences, 리소스 파일등 기능들을 저장을 해 둔 접근할 때 필요한 객체
        // activity 자체가 리소스 접근이 용이하여 context라고 말할 수 있음, 하지만 broadcastReceiver는 백그라운드에서 broadcast에서 pedning intent를 처리하므로 context를 받아와야함
        if(Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            //26 이상이면
            val notificationChannel = NotificationChannel(
                NOTIFICATION_CHANNEL_ID,
                "기상 알람",
                NotificationManager.IMPORTANCE_HIGH
            )
            // 채널 등록
            NotificationManagerCompat.from(context).createNotificationChannel(notificationChannel)
        }
    }

    private fun notifyNotification(context: Context) {
        with(NotificationManagerCompat.from(context)){
            // notification을 빌드해서 내보낼 것임
            val build = NotificationCompat.Builder(context, NOTIFICATION_CHANNEL_ID)
                .setContentTitle("알람")
                .setContentText("일어날 시간입니다.")
                .setSmallIcon(R.drawable.ic_launcher_foreground)
                .setPriority(NotificationCompat.PRIORITY_HIGH)

            notify(NOTIFICATION_ID, build.build()) // notify 설정을 해 줌
        }
    }

}
```
- 위와 같이 Broadcast Receiver를 활용하여서 알람처리를 위한 작업을 사전에 해 둠, 이렇게 설정을 하면 Broadcast Receiver를 받아서 이를 Notification으로 알람을 알려줄 수 있음
```kotlin
private fun initOnOffButton() {
        // OnOff버튼을 가져와서 클릭 리스너 설정
        val onOffButton = findViewById<Button>(R.id.onOffButton)
        onOffButton.setOnClickListener{
            // 데이터를 확인을 함(sharedpreference로 저장한)
            // tag는 object로 저장되어 있으므로 as를 통해 AlarmDisplayModel로 형변환을 해줘야함
            val model = it.tag as? AlarmDisplayModel ?: return@setOnClickListener // null 처리
            // 데이터를 저장함
            val newModel = saveAlarmModel(model.hour, model.minute, model.onOff.not())
            renderView(newModel) // onOff 기능을 바꿔줌

            // onoff에 따라 작업을 처리함
            if(newModel.onOff) {
                // 켜진 경우 -> 알람을 등록함, 캘린더를 통해 등록
                val calendar = Calendar.getInstance().apply {
                    set(Calendar.HOUR_OF_DAY, newModel.hour)
                    set(Calendar.MINUTE, newModel.minute)

                    if(before(Calendar.getInstance())) {
                        // 현재 시간을 가져와서 만약 이미 지났다면 다음날 설정하게 함
                        add(Calendar.DATE, 1)
                    }
                }
                // 알람 매니저를 가져와서 등록함
                val alarmManager = getSystemService(Context.ALARM_SERVICE) as AlarmManager
                val intent = Intent(this, AlarmManager::class.java)
                // pedningIntent를 통해서 알람 설정
                val pendingIntent = PendingIntent.getBroadcast(this, ALARM_REQUEST_CODE,
                intent, PendingIntent.FLAG_UPDATE_CURRENT)

                // 하루에 한 번씩 pendingIntent가 실행되고 알람이 됨
                alarmManager.setInexactRepeating(
                    AlarmManager.RTC_WAKEUP,
                    calendar.timeInMillis,
                    AlarmManager.INTERVAL_DAY,
                    pendingIntent
                )

            } else {
                // 꺼진 경우 -> 알람을 제거
                cancelAlarm()
            }


        }
    }
```
- 이렇게 알람을 설정하는 화면에서 알람을 설정한 것에 대해서 시스템에 등록 및 인텐트를 통해서 넘겨줌으로써 알람이 설정된 대로 BroadcastReciver가 받아서 Notification 채널을 가지고 알려주게 되는 것임

### Service
- 백그라운드에서 앱을 계속 실행하기 위한 다목적 진입점임, 오랫동안 실행되는 작업을 수행하거나 원격 프로세스를 위한 작업을 수행함
- 사용자 인터페이스를 제공하지 않음, 백그라운드 음악 재생, 네트워크를 통한 데이터를 가져오는 등
- 다른 구성 요소가 서비스를 시작한 다음 실행하도록 두거나 자신에게 바인딩하여 상호작용하게 할 수 있음
- 작업이 완료될 때까지 해당 Service를 계속 실행하라고 시스템에 지시함, 일부 데이터를 동기화하거나 사용자가 앱에서 나간 후에도 음악을 재생하는 등 처리할 수 있음
![one](/cheewr85/img/android/three.png)

- Service 또한 이러한 생명주기를 가지고 있음, Service는 기본적으로 UI가 동작하는 메인 쓰레드에서 동작하기 떄문에 Service 내에서 별도의 Thread를 생성해서 작업을 수행해야함

### 예시
- 앞서 설명했듯이 일부 데이터를 동기화 하는데 있어서 앱이 실행되지 않은 상태에서도 네트워크를 통해서 데이터를 가져올 수 있다고 하였는데 해당 예시는 위젯을 만드는데 그런 Service 사용하는 것에 대해서 알 수 있음
- 이 Service 역시 수명주기가 있기 때문에 이를 활용하여서 미세먼지 정보에 대한 위젯을 나타내기 위한 서비스 처리를 함, 포그라운드에서 서비스를 시작하고 위젯을 업뎃하는 것인데 여기서 Retrofit을 통한 네트워크 통신을 위해서 별도의 쓰레드 처리를 코루틴 스코프로 처리하여서 받고 동기화 한 것임
- 그럼 서버에 있는 미세먼지 정보를 받아서 위젯에 나타낸 것임, 이는 앱을 실행하지 않고 우리가 바탕화면에 띄우는 위젯을 나타낼 때 앱을 실행하는 것이 아니므로 아래와 같이 Service를 응용한 것
```kotlin
class UpdateWidgetService : LifecycleService() {

        override fun onCreate() {
            super.onCreate()

            createChannelIfNeeded() // API 26 이상에서 채널을 만듬
            startForeground(
                NOTIFICATION_ID,
                createNotification(),
            )
        }

        // foreground 시작을 할 때 불리는 Command
        override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {

            // 위치를 먼저 가져옴
            if (ActivityCompat.checkSelfPermission(
                    this,
                    Manifest.permission.ACCESS_BACKGROUND_LOCATION
                ) != PackageManager.PERMISSION_GRANTED
            ) {
                // 권한이 없는 경우, 위젯을 업데이트 해 줌 그에 맞게
                val updateViews = RemoteViews(packageName, R.layout.widget_simple).apply {
                    setTextViewText(
                        R.id.resultTextView,
                        "권한 없음"
                    )
                    setViewVisibility(R.id.labelTextView, View.GONE)
                    setViewVisibility(R.id.gradeLabelTextView, View.GONE)
                }
                updateWidget(updateViews)
                stopSelf() // 권한 없으면 종료함

                return super.onStartCommand(intent, flags, startId)
            }

            LocationServices.getFusedLocationProviderClient(this).lastLocation
                .addOnSuccessListener { location ->
                    lifecycleScope.launch {
                        // try-catch로 위젯 갱신 실패시 상황도 처리함
                        try {
                            // 데이터 통신을 위해서 코루틴 시작, 정보를 가져와서 갱신을 함
                            val nearbyMonitoringStation = Repository.getNearbyMonitoringStation(
                                location.latitude,
                                location.longitude
                            )
                            val measuredValue =
                                Repository.getLatestAirQualityData(nearbyMonitoringStation!!.stationName!!)
                            // 정보를 바탕으로 위뎃을 업데이트 함
                            val updateViews =
                                RemoteViews(packageName, R.layout.widget_simple).apply {
                                    setViewVisibility(R.id.labelTextView, View.VISIBLE)
                                    setViewVisibility(R.id.gradeLabelTextView, View.VISIBLE)

                                    val currentGrade = (measuredValue?.khaiGrade ?: Grade.UNKNOWN)
                                    setTextViewText(R.id.resultTextView, currentGrade.emoji)
                                    setTextViewText(R.id.gradeLabelTextView, currentGrade.label)
                                }

                            // 업데이트 하고 끝냄
                            updateWidget(updateViews)
                        } catch (exception: Exception) {
                            exception.printStackTrace()
                        } finally {
                            stopSelf()
                        }

                    }
                }

            return super.onStartCommand(intent, flags, startId)
        }

        override fun onDestroy() {
            super.onDestroy()
            stopForeground(true)
        }

        // 채널을 만듬 서비스를 위해서
        private fun createChannelIfNeeded() {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                // 해당 버전 이상인 경우 채널을 만들어야함
                (getSystemService(NOTIFICATION_SERVICE) as? NotificationManager)
                    ?.createNotificationChannel(
                        NotificationChannel(
                            WIDGET_REFRESH_CHANNEL_ID,
                            "위젯 갱신 채널",
                            NotificationManager.IMPORTANCE_LOW
                        )
                    )
            }
        }

        // Notification 생성함
        private fun createNotification(): Notification =
            NotificationCompat.Builder(this)
                .setSmallIcon(R.drawable.ic_baseline_refresh_24)
                .setChannelId(WIDGET_REFRESH_CHANNEL_ID)
                .build()

        private fun updateWidget(updateViews: RemoteViews) {
            val widgetProvider = ComponentName(this, SimpleAirQualityWidgetProvider::class.java)
            AppWidgetManager.getInstance(this).updateAppWidget(widgetProvider, updateViews)
        }
    }
```

### Intent
- Intent는 Activity, Service, Broadcast Receiver 3가지 구성요소를 비동기식 메시지로 활성화시킴, 어느 앱에 속하든 관계없이 다른 구성 요소로부터 작업을 요청하는 역할을 함
- 이것을 통해서 특정 구성 요소(명시적 Intent) 활성화할지(다른 Activity로 넘어가는 등의 Intent 처리), 아니면 구성 요소의 특정 유형(암시적 Intent)을 활성화할지 나타냄
- Intent는 Activity에 이미지를 표시하거나 웹 페이지를 열라는 요청 혹은 결과를 수신하기 위해 Activity를 시작할 수 있음, Activity도 Intent에서 결과를 반환함, Service 역시 이와 유사한 목적임
- Broadcast Receiver의 경우, 브로드캐스트될 알림을 정의함
- ContentProvider의 경우는 ContentResolver가 보낸 요청의 대상으로 지정되면 활성화 됨

### 예시
- viewPager에서 아이템 클릭시 해당 내용을 나오게 하기 위해서 아래와 같이 Intent를 통해서 보내줄 수 있음
```kotlin
private val viewPagerAdapter = HouseViewPagerAdapter(itemClicked = {
        // 아이템 클릭시 이벤트 처리 구현, 외부로 공유할 것임, 인텐트 활용
        val intent = Intent()
                .apply {
                    action = Intent.ACTION_SEND
                    putExtra(Intent.EXTRA_TEXT,"[지금 이 가격에 예약하세요!!] ${it.title} ${it.price} 사진보기 : ${it.imgUrl}") // 텍스트 값으로 정보를 내보냄(원래는 Url로 보내긴 함)
                    type = "text/plain"
                }
        // chooser를 통해 text/plain으로 모두 받게됨, 공유를 해서 위의 정보를 보낼 수 있음
        startActivity(Intent.createChooser(intent, null))
    })
```

- 그리고 위에서 Broadcast Receiver의 경우 Intent를 통해서 Broadcast를 받은 것을 볼 수 있음
- Content Provider에서도 ContentResolver로 아래와 같이 흐름으로 직접 URI 데이터에 접근해서 처리할 수 있음, 위에서는 그냥 URI를 Storage에서 받아와서 리스트에 담아서 보여주는 것이었기 때문에 이런 과정이 없이 Intent로 직접 권한을 요청하고 받아서 가져온 것임
![one](/cheewr85/img/android/four.png)

- Service의 경우 Activity와 유사하게 Intent로 처리해서 넘겨받을 수 있음

### Manifest 파일
- 위에서 설명한 이러한 앱의 구성 요소들을 모두 Manifest에서 선언을 하고 처리, 관리 할 수 있음
- Activity의 경우 `<activity>` Service의 경우 `<service>` Broadcast Receiver의 경우 `<receiver>` Content Provider의 경우 `<provider>`로 선언함
- 이외에도 Manifest에서 앱이 요구하는 모든 사용자 권한을 식별하고 앱에서 요구하는 최소 API 레벨을 선언함
- 그리고 앱에서 사용하거나 요구하는 하드웨어 및 소프트웨어 기능(카메라, 블루투스 서비스 등), 앱이 링크되어야 하는 API 라이브러리(Google Maps)를 선언함
