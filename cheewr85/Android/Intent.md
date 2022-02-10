## Intent
- 다른 앱 구성 요소로부터 작업을 요청하는데 사용할 수 있음, 구성 요소 사이의 통신을 할 수 있음

- 각각, Activity, Service, Broadcast등에서 Intent를 통해서 상호작용을 함

### Explicit Intent
- Activity, Service 클래스를 시작하고 응답하면서 Background에 파일을 다운로드 하는 등 흔히 쓰는 방식으로 Intent를 사용함

#### 예시
- 예를 들면 아래와 같이 DownloadService를 구축했을 때 웹상에서 파일을 다운로드 하도록 함

- 그러면 아래와 같이, Intent를 통해서 Context와 Class 객체로 명시적으로 Intent를 처리함
```kotlin
// Executed in an Activity, so 'this' is the Context
// The fileUrl is a string URL, such as "http://www.example.com/image.png"
val downloadIntent = Intent(this, DownloadService::class.java).apply {
    data = Uri.parse(fileUrl)
}
startService(downloadIntent)
```

- 이외에도 아래와 같이 데이터를 넣어서 보내줄수도 있음, Main에서 데이터를 받아와 Map에다가 보내는 경우임, 그러면 Map에서도 이를 받아서 해당 데이터를 처리함
```kotlin
adapter.setSearchResultList(dataList) {
            Toast.makeText(this,"빌딩이름 : ${it.name} 주소 : ${it.fullAddress} 위도/경도 : ${it.locationLatLng}", Toast.LENGTH_SHORT).show()
            startActivity(
                Intent(this, MapActivity::class.java).apply {
                    putExtra(SEARCH_RESULT_EXTRA_KEY, it)
                } // 인텐트로 구글맵 보이게 실행함, 그러면서 위치 정보에 대해서 담아서 보내줌
            )
        }
```
```kotlin
if (::searchResult.isInitialized.not()) {
            // 초기에 값이 없다면 그 값을 가져와서 넣어줌
            intent?.let {
                // 인텐트로 넘겨진 값을 받아옴
                searchResult = it.getParcelableExtra<SearchResultEntity>(SEARCH_RESULT_EXTRA_KEY)
                    ?: throw Exception("데이터가 존재하지 않습니다.")
                setupGoogleMap() // 구글맵 가져와서 지정함
            }
        }
```
### Implicit Intent
- Explicit Intent와는 다르게 일반적인 작업을 선언하여 해당 기능을 처리할 수 있도록 Intent를 사용함

- 이 부분에 대해서는 Android 시스템에서 적절한 요소를 씀, 이 때 Manifest 파일에서 선언된 IntentFilter와 비교해서 시스템에서 해당 구성 요소를 시작해 이를 Intent 객체를 통해서 처리함

#### 예시
- 여기서 본인의 앱은 작업을 수행할 수 없지만 다른 앱은 그 작업을 수행할 수 있으면 그 앱을 선택하게 할 경우 암시적 인텐트가 유용함

- 아래와 같이 공유할 컨텐츠가 있을 경우 이를 Intent로 호출하여서 어느 앱을 통해서 공유할 것인지 처리할 수 있음
```kotlin
// Create the text message with a string
val sendIntent = Intent().apply {
    action = Intent.ACTION_SEND
    putExtra(Intent.EXTRA_TEXT, textMessage)
    type = "text/plain"
}

// Verify that the intent will resolve to an activity
if (sendIntent.resolveActivity(packageManager) != null) {
    startActivity(sendIntent)
}
```

- 여기서 대표적으로 본다면 `ACTION_VIEW`의 경우 갤러리 앱에서 볼 사진이나 지도 앱에서 볼 주소라던지 `ACTION_SEND`를 통해서 이메일 앱 또는 소셜 공유 앱으로 활용한다던지의 예시를 볼 수 있음, 활용도는 위와 같은 코드로 똑같이 사용하되 Intent로 처리해주면 됨

### Intent Filter
- Implicit Intent를 사용하기 위해서 Intent-Filter를 사용해서 처리할 수 있음

- 여기서 물론 Manifest에서 설정을 해서 처리를 해 줄 수 있지만, 이 경우 인텐트 작업, 데이터 및 카테고리를 통해서 어느 유형의 인텐트를 수락할 지 지정을 함

- 이 부분은 어느정도 정해진 것이 있고 공통적으로 정의되서 사용을 하기만 하는 부분이 상당히 많이 있음, 예시로써 아래의 공식문서로 가장 공통적인 부분으로 볼 수 있는 부분이 정의되어 있음

- https://developer.android.com/guide/components/intents-common?hl=ko

### Intent Service
- Intent를 전달하여 서비스의 어떤 작업을 수행하는데 사용함, 주로 파일 다운로드나 업로드 등의 처리 시간이 긴 작업을 수행하는데 사용을 함, Intent만을 전달해서 사용하면 됨

- 즉, 서비스와의 통신을 위해서 쓴다고 봐도 무방함

- 위의 예시와 같이 파일 다운로드 하는 것도 있고 아래와 같이 IntentService를 상속받아서 Thread를 실행시켜서 체킹하는 경우에도 사용됨

```kotlin
val intent = Intent(this, MyIntentService::class.java)
intent.putExtra("MSG", "Do something")
startService(intent)
```

- 이 부분을 짚고 넘어가는 것은 흔히 Intent의 경우 Activity에서만 사용, 다른 Activity로 넘어가기 위해서 혹은 데이터를 전달하고 처리하는데에 집중되어 있는데 이렇게 처리 시간이 긴 작업의 경우 Service를 통해서 처리할 수 있게끔 사용함
 