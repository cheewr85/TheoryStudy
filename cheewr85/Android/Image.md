# 이미지 처리
- 여기서 다룰 부분은 단순하게 이미지를 말하는 것이 아님

- 일반적인 접근법으로 ImageView를 쓰는것 Drawable을 추가하는 것, 네트워크로 받아오는 것 1차원적으로 단순하게 생각할 순 있음

- 여기서 중요한 건 이런 이미지 처리를 가볍게 여기고 쓴다면 많은 이미지를 사용하거나 고해상도 이미지를 ImageView에 로드해야하는 경우 메모리 부족이 발생함

- 그 이유는 안드로이드 앱 내에서 사용할 수 있는 메모리가 정해져 있기 때문에 이를 간과하게 된다면 메모리 오류가 발생함

## Bitmap Caching
- 이런식으로 이미지 구성과 로드에 대해서 잘 생각해야하는데 그 개선 방안 중에 하나가 캐싱을 하는 것임

- 여기서 Memory Cache를 사용하여서 앱 내에 존재하는 메모리에 Bitmap을 캐싱하여 필요할 때 빠르게 접근할 수 있음, 하지만 용량이 커질 수 있기 때문에 그 부분을 고려해야함

- 이 용량 처리 문제와 앱이 백그라운드로 전환됐을 때도 삭제되지 않기를 바란다면 Disk Cache를 생각할 수 있음, 단 Memory보단 로드하는 시간이 더 걸림

## BitmapPool
- 이를 좀 더 상세하게 본다면 사용하지 않는 Bitmap을 리스트에 넣고 추후에 동일한 이미지를 로드할 때 다시 메모리에 적재하지 않고 pool에 있는 이미지를 가져와 재사용하는 것임

- 이렇게 처리한다면 비트맵 객체를 내부 알고리즘에 의해서 로드하기가 수월해지고 앞서 말했던 문제들을 어느정도 해결할 수 있음

- 자세한 사항은 해당 링크 참조
- https://velog.io/@jshme/Android-Hello-Out-Of-Memory

- 이렇게 직접적으로 이미지를 ImageView에 바로 로드하면서 처리한다고 할 경우, 물론 몇 몇 간단한 이미지와 리소스가 적으면 큰 문제는 되지 않을 수 있겠지만 이를 감수하면서까지 하나로 밀어붙이는데 있어서 고려해야할 요소가 상당히 많아짐 

- 그래서 라이브러리를 활용함으로써 이러한 문제를 처리하도록 하는 것임

## 이미지 로더 라이브러리
- 위에서 언급한 문제들로 단순하게 이미지를 로드하려고 하면 고려할 요소가 많아짐

- 그렇기 때문에 이를 라이브러리를 통해서 해결을 할 수 있음

### Picasso
- 해당 라이브러리는 최소한의 메모리로 이미지의 다양한 Transformation을 지원하고, 자동으로 메모리 & 디스크 캐싱, 어댑터에서 ImageView를 재활용 및 다운로드 취소가 가능함

- 원본 이미지 크기를 그대로 비트맵에 그린 후에 ImageView에 적용함, 그래서 고화질 이미지 로드에 문제가 생길수도 있음

- 그리고 GIF 형식은 지원하지 않음

- 예시
```java
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
```java
 Picasso.get()
        .fit()
        .transform({...})
        .load(url)
```

### Glide
- Google에서 만든 이미지 로더 라이브러리, 빠른 이미지 로딩, 버벅 거림과 끊김 현상이 발생하지 않음

- Volley, OkHttp 라이브러리에 연결할 수 있는 유틸리티 라이브러리도 존재해서 자주 사용하고 있는 라이브러리와 호환성이 좋음

- ImageView의 크기를 측정한 다음 원본이미지를 가져와 ImageView 크기에 맞게 리사이징 후 비트맵에 그려주는 것을 기본 옵션으로 함

- 예시
```java
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

### Coil
- Coroutine Image Loading의 줄임말로 코틀린 & 코루틴으로 구성됨

- 라이브러리가 거의 100% 코틀린으로 이루어져 있어서 AndroidX, OkHttp 등 현업에서 많이 쓰이는 라이브러리들을 지원하고 있음

- Glide를 벤치마킹했고 ImageView 확장함수 지원, 코틀린의 함수형 언어의 장점을 활용할 수 있음

```kotlin
imageView.load(url) {
            listener(
                    onError = { _,_ -> /** Show toast. */ },
                    onSuccess = { _,_ -> toast("Complete") }
                    }
            )
            transformations(RoundedCornersTransformation(128f))
        }
```