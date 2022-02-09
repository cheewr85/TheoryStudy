# Handling Image

ImageView를 통해 이미지를 로딩할 때 가장 많이 맞닥드리는 세 가지 이슈

## Out of memory error.

OOM, 메모리 부족
![](https://media.vlpt.us/images/jshme/post/49b4df11-5ceb-4492-9d4f-f40655655291/IMG_06A0242D2ABE-1.jpeg)

- Dalvik Heap 영역 : java 객체 저장 메모리, 꽉 차면 확장되지만 줄지 않음

- External 영역 : native heap의 일종으로 네이티브의 비트맵 객체를 저장하는 메모리, 유동적인 공간 확장, 축소 Dalvik heap footprint + External limit > memory limit per process -> OOM

- HoneyComb 이상부터는 External Heap이 안드로이드 메모리 시스템에서 제거되어 Dalvik 영역만 신경써주면 됨

- External 대신 Dalvik heap이 비트맵 메모리를 다룰 수 있게 되었고 GC에도 접근이 가능해짐

- 단일 고해상도 이미지 처리는 **BitmapFactory** 객체를 이용해 다운샘플링과 디코딩 방식 중 선택하면 충분

- 다중 이미지 처리는 **이미지 캐싱**이 필요

### Bitmap Caching

- 이미지가 화면에서 사라졌다가 다시 나올 때마다 매번 새로 로드하는 것은 UX 측면에나 성능 측면에나 좋지 않음

- 메모리와 디스크 캐시를 이용하면 로드 시간이 단축됨
  
  - ### Memory Cache
    
    앱 내에 존재하는 메모리에 비트맵을 캐싱하고 필요할 때 빠르게 접근
    이곳에 저장되면 앱 용량에 포함됨 -> 큰 비트맵은 Disk Cache 이용
    
    ##### BitmapPool
    
    사용하지 않는 Bitmap을 리스트에 넣어두고 추후 필요할 때 재사용
    
    - LRU 캐싱 알고리즘으로 구현된 LinkedList(lruBitmapList)와 Byte Size 순으로 정렬된 LinkedList(bitmapList) 사용
    
    - LRU 알고리즘 
      오랫동안 참조되지 않은 비트맵 객체는 맨 뒤로 밀리고 맨 뒤의 객체 회수
      ![Implement Least Recently Used (LRU) Cache](https://iq.opengenus.org/content/images/2018/06/lru-1.jpg)
      
  
  - ### Disk Cache
    
    Memory Cache에 넣기에는 너무 많은 캐시가 필요하거나 앱이 백그라운드 상태여도 캐시가 유지되어야 하면 Disk Cache 이용
    Memory Cache에 비해서 로드 속도는 오래 걸림



## Slow loading of an image into the view.

비트맵을 뷰에 로드할 때 로딩이 느려지는 현상
이미지가 로드될 때 그 밖에 다른 작업들이 진행 중이어서 이미지 로딩이 후순위로 밀리는 것

## UI becomes unresponsive. Not smooth scrolling.

화면 스크롤이 버벅거리는 등 UI가 점차 둔해짐
앱이 메인 스레드에서 너무 많은 작업을 처리하고 있기 때문
안드로이드는 매 **16ms** 마다 UI를 업데이트 함
작업이 16ms 이상 소요될 경우, 업데이트를 스킵, 즉 프레임이 스킵됨
프레임 스킵은 화면의 **버벅거림**을 초래함 -> **FPS(Frame Per Second)** 저하
이때 이미지를 백그라운드에서 불러오고 있다고 하더라도 UI 렉은 지속
이는 처리되고 있는 비트맵의 크기가 너무 커서 가비지 컬렉터(GC)가 자주 실행되기 때문임 -> GC 실행 = 앱 정지



## Image Loader Library

Picasso, Glide, Fresco, Coil 中 상황에 따라 선택

### Picasso

- OkHttp, Retrofit 등의 라이브러리를 만든 Square 라이브러리

- 최소한의 메모리를 활용해 다양한 이미지 변형 기능 제공

- 자동으로 메모리 & 디스크 캐싱

- 어댑터에서 ImageView 재사용, 다운로드 취소 가능

- ```kotlin
  Picasso.get().load(url)
                  .transform(RoundedCornersTransformation(128, 3))
                  .into(imageView5, object : Callback {
                      override fun onSuccess() {
                          toast("Complete")
                      }
  
                      override fun onError(e: Exception?) {
                          TODO("Not yet implemented")
                      }
                  })
  ```

### Glide

- 구글 라이브러리

- 빠른 이미지 로딩

- 버벅거림과 끊김 현상 방지

- Picasso 사용 방식과 유사

- Volley / OkHttp 라이브러리에 연결 가능한 유틸리티 라이브러리를 제공하여 호환 좋음

- 썸네일 보기, GIF 로딩, 동영상 스틸 보기 등 타 라이브러리와 차별화된 기능 제공

- ```kotlin
  Glide.with(this)
                  .load(url)
                  .apply(RequestOptions.bitmapTransform(RoundedCornersTransformation(128, 3)))
                  .listener(object : RequestListener<Drawable> {
                      override fun onLoadFailed(e: GlideException?, model: Any?, target: Target<Drawable>?, isFirstResource: Boolean): Boolean {
                          TODO("Not yet implemented")
                      }
  
                      override fun onResourceReady(resource: Drawable?, model: Any?, target: Target<Drawable>?, dataSource: DataSource?, isFirstResource: Boolean): Boolean {
                          toast("Complete")
                          return false
                      }
                  })
                  .into(imageView)
  ```

### Fresco

- 페이스북 라이브러리

- Glide와 비슷한 포지션

- Fresco의 ImageView를 사용해야하는 것이 단점

- 웹 상 이미지 스트리밍에 초점

## Coil

- Coroutine Image Loader의 약자로 Kotlin Coroutine으로 만들어진 라이브러리

- Glide, Fresco보다 상대적으로 가벼움

- Glide를 벤치마킹하여 기능적으로 유사

- Kotlin 기능을 활용하여 Kotlin 사용자들이 사용하기 편함(함수형 언어 사용으로 간결한 코드)

- Coroutines, OkHttp, Okio, AndroidX Lifecycles등의 최신 라이브러리 사용

- 아직 정식 출시는 아니어서 실무에서 활용 적음

- 안드로이드 SDK 최신 버전에서 사용 권장

- 어느정도의 코루틴 개념 학습 필요

- ```kotlin
   imageView.load(url) {
              listener(
                      onError = { _,_ -> /** Show toast. */ },
                      onSuccess = { _,_ -> toast("Complete") }
                      }
              )
              transformations(RoundedCornersTransformation(128f))
          }
  ```


## References

https://velog.io/@jshme/Android-Hello-Out-Of-Memory

[How The Android Image Loading Library Glide and Fresco Works?](https://blog.mindorks.com/how-the-android-image-loading-library-glide-and-fresco-works-962bc9d1cc40)

[[android] Bitmap 과 메모리에 대한 이야기. 끝판왕](https://aroundck.tistory.com/2535)

[picass vs glide vs fresco 안드로이드 이미지 라이브러리 비교](https://jinsangjin.tistory.com/11)

[[Android] 이미지 로딩 라이브러리 - Coil :: 코딩하는후운](https://hooun.tistory.com/26)

[android - Picasso vs Imageloader vs Fresco vs Glide | Hashcode](https://hashcode.co.kr/questions/1074/picasso-vs-imageloader-vs-fresco-vs-glide)
