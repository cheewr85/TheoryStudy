# 커스텀 뷰
- 개발하는 애플리케이션의 요구 조건에 따라 화면 구성 요소에 요구하는 바도 조금씩 차이가 발생할 수 있음, 이를 안드로이드 프레임워크에서 제공하는 API를 사용하여 화면이 구성된다면 좋지만 이런 적절한 뷰가 없을 때 커스텀 뷰를 만들어서 사용할 수 있음

- View 서브 클래스를 직접 만들 수 있음, 해당 위젯(Button, TextView, EditText... 등)과 레이아웃을 서브클래스로 만들고 그 메서드를 재정의 할 수도 있음

- 이렇게 직접 만들면 화면 요소의 모양과 기능을 정밀하게 제어할 수 있음

- 완전히 맞춤 랜더링 된 뷰 유형을 만들거나 View 구성요소 그룹을 새로운 단일 구성요소로 결합할 수 있고 구성요소가 화면에 랜더링되는 방법을 재정의할 수 있음

- 그리고 키 누름과 같은 다른 이벤트를 캡처하고 맞춤 방식으로 처리할 수 있음

## 기본 접근법
- 자체 클래스로 기존의 View 클래스 또는 서브클래스를 확장함

- 슈퍼클래스에서 일부 메서드를 재정의함, 재정의할 메서드는 on으로 시작함(onDraw, onMeasure, onKeyDown등)

- 새 확장 클래스를 사용함, 완료되면, 기반이 되었던 뷰 대신 새 확장 클래스를 사용할 수 있음

### 완전히 맞춤설정된 구성요소
- 이 케이스의 경우 원하는 모양의 그래픽 구성요소를 만들어 커스텀 할 수 있음, 이 상황에서는 거의 확실하게 onMeasure와 onDraw를 재정의해야함, 왜냐하면 기존에 쓰는 View를 재정의해서 내가 원하는대로 커스텀하기 때문에 그럼

- 여기서 onDraw의 경우 Canvas에서 제공하는데 이를 통해서 그래픽, 기타 표준 맞춤 구성요소, 스타일이 지정된 텍스트등 원하는 것 모두 구현이 가능함

- onMeasure에 경우는 조금 복잡하게 들어감, 이는 구성요소와 컨테이너 간 렌더링의 중요한 부분이라서 포함된 부분의 측정값을 효율적으로 정확하게 하기 위해서 onMeasure를 재정의해야함

- 이는 setMeasuredDimension등 메서드 호출을 위한 요구 사항(계산된 후 측정된 너비 및 높이 사용)에 의해 좀 더 복잡해짐

- 즉, 요약해서 정리하자면 이를 커스텀하기 위해서 해당 사양, 너비와 높이를 호출해서 이를 제한하고 계산하는 등 클리핑, 스크롤 등 이렇게 계산된 측정값을 다 설정을 해줘야하는 것임, 만약 그렇게 하지 않으면 예외가 발생해버림

### 복합 컨트롤
- 기존 컨트롤 그룹으로 구성된 재사용 가능한 구성요소를 조합하려는 경우 복합 구성요소를 만드는게 좋음

- 더 작은 많은 컨트롤(또는 뷰)을 하나로 취급할 수 있는 논리적 항목 그룹으로 만드는 것

- 즉 일종의 레이아웃으로 xml 파일에 직접 이를 만든 다음에 이를 CustomView 만들듯이 레이아웃을 상속받아서 복합 컨트롤 자체를 xml의 요소로 쓸 수 있게끔 커스텀 뷰로 만드는 것임

### 기존 뷰 유형 수정
- 원하는 것과 이미 매우 유사한 구성요소가 있는 경우 이 구성 요소를 확장하고 변경하려는 동작만 재정의해서 쓸 수 있음

- 클래스를 맞춤설정을 위한 뷰를 상속 받은 다음에 클래스를 초기화한 뒤 원하는 요소를 재정의하면 됨

- 그리고 해당 클래스에 대해서 xml의 구성요소로써 사용할 수 있음

#### 예시
- 3번째 사항에 해당한다고 볼 수 있음, ImageButton을 사용한 것인데 이는 state에 따라서 바꾸기 위해서 기존에 View의 유형에서 수정을 한 것임
```kotlin
package techtown.org.recorder

import android.content.Context
import android.util.AttributeSet
import androidx.appcompat.widget.AppCompatImageButton

/*
CustomView로써 xml 파일에서 수정을 하게 하기 위한 클래스
그러면 이제 이 클래스에서 상태에 따라 UI를 수정하게끔 할 수 있음, 아래와 같이 ImageButton을 상속받고
미리 정의한 State 상태에 따라서 버튼을 바꾸면 됨
 */
class RecordButton( // CustomView로 활용하기 위해서 아래와 같이 파라미터를 기본적으로 받아줘야함
        context: Context,
        attrs: AttributeSet
): AppCompatImageButton(context,attrs) {

    init{
        // 동그란 버튼으로 하기 위해서 초기화를 함
        setBackgroundResource(R.drawable.shape_oval_button)
    }
    // State에 따라서 recordButton의 아이콘을 변경함
    // 앞서 정의한 State 클래스를 매개변수로 받아와서 when 분기문을 통해서 상태에 따라 Vector Asset으로 추가한 아이콘을 변경함
    fun updateIconWithState(state: State) {
        when(state) {
            State.BEFORE_RECORDING -> {
                setImageResource(R.drawable.ic_record)
            }
            State.ON_RECORDING -> {
                setImageResource(R.drawable.ic_stop)
            }
            State.AFTER_RECORDING -> {
                setImageResource(R.drawable.ic_play)
            }
            State.ON_PLAYING -> {
                setImageResource(R.drawable.ic_stop)
            }
        }
    }
}
```

- 그리고 녹음이 잘 되는지에 대한 커스텀을 1번째 사항처럼 완전 맞춤형으로 하기 위해서 직접 너비 폭 그리고 상태에 대해서 다 재정의를 하고 그리는 것까지 다시 다 처리를 함
```kotlin
package techtown.org.recorder

import android.content.Context
import android.graphics.Canvas
import android.graphics.Paint
import android.util.AttributeSet
import android.view.View

/*
녹음이 잘 되고 있는지 녹음하는 부분에 대해서 시각화를 하기 위한 CustomView
 */
class SoundVisualizerView(
        context: Context,
        attrs: AttributeSet? = null
) : View(context, attrs) {

    // 이를 통해 현재 Amplitude에 대해서 메인에서 받을 수 있음
    var onRequestCurrentAmplitude: (() -> Int)? = null

    // 음향 진폭을 시각화하고 Paint로 그릴 것임
    private val amplitudePaint = Paint(Paint.ANTI_ALIAS_FLAG).apply{
        // 어떤 색으로 그릴지 지정
        color = context.getColor(R.color.purple_500)
        // 길이 지정
        strokeWidth = LINE_WIDTH
        // 라인의 양 끝을 Round 처리함
        strokeCap = Paint.Cap.ROUND
    } // 곡선이 좀 더 부드럽게 그려짐 ANTI_ALIAS
    // 그려야 할 사이즈 정해야함, 리소스를 많이 차지하므로 크기에 대해서는 미리 정하고 제대로 처리해야함
    private var drawingWidth: Int = 0
    private var drawingHeight: Int = 0
    // 리스트로 진폭을 저장하고 드로잉에 쓰기 위한 리스트
    private var drawingAmplitudes: List<Int> = emptyList()

    // 다시 플레이를 할 때도 시각화를 위해 선언, Replaying인지 확인하기 위해서 true/false로 구분
    private var isReplaying: Boolean = false
    private var replayingPosition: Int = 0



    // 시간이 지나가면서 흐르듯이 움직이는 듯한 효과를 줘야함, 실행시 그릴때 20프레임으로 움직이면서 스무스하게 녹음이 되듯이 오른쪽으로 녹음창이 흐름
    private val visualizeRepeatAction: Runnable = object : Runnable {
        override fun run() {
            if(!isReplaying) {
                val currentAmplitude = onRequestCurrentAmplitude?.invoke()
                        ?: 0 // 메인에 bindViews에 있는걸 가져옴, 그럼 오디오에 maxAmplitude가 들어옴(메인에서 설정했으므로)
                // Amplitude를 가져오고 Draw를 요청함
                drawingAmplitudes = listOf(currentAmplitude) + drawingAmplitudes // 순차적으로 넣기 위해서 설정함
            } else {
                // replaying시 멈춘 순간에서 다시 그 포지션 기준으로 추가하는 것이므로 Position을 더함
                replayingPosition++
            }
            invalidate() // onDraw를 다시 호출하기 위해, 갱신을 위해서
            handler?.postDelayed(this, ACTION_INTERVAL)
        }
    }

    override fun onSizeChanged(w: Int, h: Int, oldw: Int, oldh: Int) {
        super.onSizeChanged(w, h, oldw, oldh)
        // 사이즈가 깨지면 안되고 제대로 처리해야하므로 들어온 값에 대해서 설정을 해줌, 이러면 깨질일이 없음
        drawingWidth = w
        drawingHeight = h
    }

    override fun onDraw(canvas: Canvas?) {
        super.onDraw(canvas)
        // 진폭을 그리기 위해서 onDraw를 오버라이딩함, 진폭 표시는 캔버스에 선을 긋고 길이와 선 사이의 간격을 그리게끔 요청할 것임(무엇을 그릴건지)
        // 그다음 Paint에 어떤 굵기, 색깔이 있는지 처리해줘야함
        // Height는 실제 값에 기반해야함, 클 때는 길게 작을 때는 짧게 나오므로
        // Amplitude, 볼륨에 맞춰서 그 길이를 지정함

        canvas ?: return

        // 선에 중앙값을 그림(길이의 절반)
        val centerY = drawingHeight / 2f
        // 수많은 진폭값을 리스트로 저장하고 하나씩 오른쪽으로 채워나가면서 그릴 것임
        // 시작 포인트는 그리는 영역에 오른쪽 가로길이
        var offsetX = drawingWidth.toFloat()

        // 리스트에 담긴 것을 하나씩 그림
        drawingAmplitudes
                .let{
                    // 만약 Replaying 하는 상태라면 위에서 저장한 것 기준으로 그 다음 추가해서 그림
                    // 아닌 경우는 그냥 그리면 됨
                    amplitudes ->
                    if(isReplaying){
                        amplitudes.takeLast(replayingPosition)
                    } else {
                        amplitudes
                    }
                }
                .forEach { amplitude ->
            // 실제 그릴 length, 현재 진폭값에서 최대값을 나누고 그릴려는 높이를 전달하면 높이 대비 몇 % 그릴지 알 수 있음
            val lineLength = amplitude / MAX_AMPLITUDE * drawingHeight * 0.8F // 100일 경우 꽉차는 것을 대비하기 위해 0.8을 곱함

            // LINE_SPACE만큼 감소시키며 계속 우측으로 차례대로 그리므로
            offsetX -= LINE_SPACE

            // 다 못 그리는 왼쪽을 넘어가는 영역이 있을 수 있음, 뷰를 넘어서는 부분
            if(offsetX < 0) return@forEach

            // 시작과 끝을 전달받고 점을 찍고 종료하는 시점과 어떻게 그릴지 설정
            canvas.drawLine(
                    offsetX,
                    centerY - lineLength / 2F,
                    offsetX,
                    centerY + lineLength / 2F,
                    amplitudePaint
            )
        }

    }

    // 반복해서 호출하면서 그리게 됨
    fun startVisualizing(isReplaying: Boolean) {
        this.isReplaying = isReplaying
        handler?.post(visualizeRepeatAction)
    }

    // 반복 호출을 멈추는 메소드
    fun stopVisualizing() {
        // 여러번 다시 재생할 때 반복을 위해서
        replayingPosition = 0
        handler?.removeCallbacks(visualizeRepeatAction)
    }

    // 기존의 Drawing을 초기화하는 메서드
    fun clearVisualization() {
        drawingAmplitudes = emptyList()
        invalidate()
    }

    companion object {
        // 그리려고 하는 값은 미리 정하고 알아야 하기 때문에 상수로 저장
        private const val LINE_WIDTH = 10F
        private const val LINE_SPACE = 15F
        // 최대 볼륨에 대해서 지정함
        private const val MAX_AMPLITUDE = Short.MAX_VALUE.toFloat() // 0이 되는 현상을 방지하기 위해서 Float으로 바꿈
        private const val ACTION_INTERVAL = 20L
    }
}
```

- 이를 사용하는데 있어서 View를 xml에 추가하고 class 속성을 통해서 추가할 수 있지만 그 구성요소 자체를 아예 받을 수 있음

- 아래와 같이 클래스명 자체로 구성요소로 추가해서 넣음
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <techtown.org.recorder.SoundVisualizerView
        android:id="@+id/soundVisualizerView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginBottom="10dp"
        app:layout_constraintBottom_toTopOf="@id/recordTimeTextView"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <Button
        android:id="@+id/resetButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="RESET"
        app:layout_constraintBottom_toBottomOf="@id/recordButton"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@id/recordButton"
        app:layout_constraintTop_toTopOf="@id/recordButton"
        tools:ignore="HardcodedText" />

    <techtown.org.recorder.CountUpView
        android:id="@+id/recordTimeTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:text="00:00"
        android:textColor="@color/white"
        app:layout_constraintBottom_toTopOf="@id/recordButton"
        app:layout_constraintLeft_toLeftOf="@id/recordButton"
        app:layout_constraintRight_toRightOf="@id/recordButton"
        tools:ignore="HardcodedText" />

    <!--앞서 클래스로 정의한 RecordButton을 가져와서 사용할 수 있음-->
    <techtown.org.recorder.RecordButton
        android:id="@+id/recordButton"
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:layout_marginBottom="50dp"
        android:padding="25dp"
        android:scaleType="fitCenter"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```