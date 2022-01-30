# RecyclerView
- 리사이클러뷰를 이론적으로 정의하면 다음과 같음

- 사용자가 관리하는 많은 수의 데이터 집합을 개별 아이템 단위로 구성하여 화면에 출력하는 ViewGroup이며, 한 화면에 표시되기 힘든 많은 수의 데이터를 스크롤 가능한 리스트로 표시해주는 위젯

- 여기서 리사이클러뷰 이전에 리스트뷰를 사용했는데 이 리스트뷰보다도 더 유연하고 성능적인 측면에서 많이 개선됨

- 여기서 리사이클러뷰는 리사이클러라는 의미 그 자체로 아이템 표시를 위해 생성한 뷰를 재활용을 함, 그러기 위해서 뷰홀더가 필수 구현 사항으로 만들어져서 유연함을 충분히 고려할 수 있게함

- 즉, 구현 요소 또는 구현에 따른 결과물이 쉽게 변경되거나 확장될 수 있음을 의미함

## 구성요소
- 데이터 목록을 아이템 단위의 뷰로 구성하여 화면에 표시하기 위한 어댑터를 사용함

- 또한 아이템뷰가 나열되는 형태를 관리하기 위한 요소를 제공하는데 이를 레이아웃 매니저라고 함

- 그리고 어댑터를 통해 만들어진 각 아이템 뷰는 뷰홀더 객체에 저장되어 화면에 표시되고 필요에 따라 재활용 됨

- 즉 아래와 같은 흐름으로써 활용이 됨

![one](/cheewr85/img/seventeen.png)

- 여기서 자세하게 RecyclerView를 활용하여서 쓰는 방법에 대해서는 별도로 실습을 통해서 하는걸 권장, 위의 흐름을 이해하고 진행하는게 중요

### ViewHolder 패턴
- 여기서 ViewHolder를 쓴다고 하였는데 이는 각 View를 보관하는 객체로, 각 구성요소를 저장하여 반복적으로 조회하지 않고도 즉시 액세스 할 수 있는것을 의미함

- 이 패턴이 나온 것은 일반적으로 안드로이드에서 View의 구성요소 호출 시 findViewById등을 사용하지만 리사이클러뷰와 같은 리스트는 그런 작업 자체가 힘드므로 각 View 객체를 ViewHolder에 보관함으로써 그러한 구성요소 호출을 줄여줄 수 있는 것을 의미함

- 그래서 RecyclerView에서 RecyclerView.Adapter를 상속을 받는데 이를 호출하면서 `onCreateViewHolder`와 `onBindViewHolder`를 이용함

- `onCreateViewHolder`를 통해서 각 아이템을 위한 XML 레이아웃을 이용한 뷰 객체를 생성하고 뷰홀더에 담아 리턴을 함 그리고 `onBindViewHolder`의 경우 ViewHolder를 데이터와 연결할 때 사용을 해 이 메서드를 통해 ViewHolder의 레이아웃을 채움

- 이를 코드적으로 본다면 아래와 같음

- 즉 위의 설명과 같이 우선 ViewHolder를 내부 클래스로 만들어서 데이터를 바인딩을 처리하고 그 다음 `onCreateViewHolder`를 통해서 View Binding을 활용하여서 XML 레이아웃을 View 객체를 생성해서 ViewHolder에 담아 리턴을 한 것이고 `onBindViewHolder`를 통해서 내부에 있는 bind함수를 통해서 데이터 바인딩을 처리하게 메서드를 만들어두었는데 이를 활용하여 데이터를 연결함과 동시에 bind 함수에 있는 것처럼 ViewHolder의 레이아웃에 내용을 표시하게 처리를 하는 것임
```kotlin
package techtown.org.retailmarket.chatdetail

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.DiffUtil
import androidx.recyclerview.widget.ListAdapter
import androidx.recyclerview.widget.RecyclerView
import techtown.org.retailmarket.databinding.ItemChatBinding
import techtown.org.retailmarket.databinding.ItemChatListBinding

class ChatItemAdapter: ListAdapter<ChatItem, ChatItemAdapter.ViewHolder>(diffUtil){

    inner class ViewHolder(private val binding: ItemChatBinding): RecyclerView.ViewHolder(binding.root) {

        fun bind(chatItem: ChatItem) {
            binding.senderTextView.text = chatItem.senderId
            binding.messageTextView.text = chatItem.message
        }

    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        return ViewHolder(ItemChatBinding.inflate(LayoutInflater.from(parent.context), parent, false))
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.bind(currentList[position])
    }

    companion object {
        val diffUtil = object: DiffUtil.ItemCallback<ChatItem>() {
            override fun areItemsTheSame(oldItem: ChatItem, newItem: ChatItem): Boolean {
                // 새로운 아이템이 같은지 비교, 키값이 필요한데 현재 모델에서는 createdAt을 키값으로 둠
                return oldItem == newItem
            }

            override fun areContentsTheSame(oldItem: ChatItem, newItem: ChatItem): Boolean {
                // 현재 노출 아이템과 새로운 아이템이 같은지 비교
                return oldItem == newItem
            }

        }
    }
}
```

- 결과적으로 이러한 매커니즘을 기반으로 움직이는 것을 이해하고 활용하면 됨