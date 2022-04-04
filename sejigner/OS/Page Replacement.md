# Page Replacement
> 페이지 교체 알고리즘 : 필요한 페이지가 주기억장치에 적재되지 않았을 때 어떤 프레임을 비우고 어떤 페이지를 넣을지 결정하는 알고리즘

## 페이지 교체 알고리즘의 종류
- OPT - Optimal : 앞으로 가장 오랫동안 사용되지 않을 페이지 교체
- FIFO - First In First Out
- LRU - Least recently used: 가장 오랫동안 사용되지 않은 페이지 교체
- LFU - Least frequently used: 참조 횟수가 가장 작은 페이지 교체
- MFU - Most frequently used: 참조 횟수가 가장 많은 페이지 교체
- NUR - Not used recently: 최근에 사용하지 않은 페이지 교체

### OPT (최적 교체)
![opt](/sejigner/img/os/page replacement/opt.png)
- 가장 오랫동안 사용되지 않을 페이지를 교체하는 방법
- 가장 이상적이고 효율적이지만 페이지의 참조 상황을 미리 예측해야 하므로 가능성이 희박

### FIFO (선입선출)
![opt](/sejigner/img/os/page replacement/fifo.png)
- 먼저 들어온 페이지를 먼저 내보내는 방법
- 간단하고, 초기화 코드에 적절
- 들어온 시간이나 순서를 큐에 저장하여 활용
- 페이지 수가 많으면 페이지 결함의 횟수가 감소할 것으로 예상되지만 꼭 그렇지는 않음

### LRU(Least recently used)
![opt](/sejigner/img/os/page replacement/lru.png)
- 오랫동안 사용하지 않은 데이터는 앞으로도 사용하지 않을 확률이 높다고 가정  (시간 지역성 temporal locality)
- 사용된 시간을 알 수 있는 부분을 저장하여 가장 오랫동안 참조되지 않은 데이터 파악 (페이지마다 카운터 작동)
- 프로세스가 메모리에 접근할 때마다 사용된 시간을 측정하고, 페이지의 카운터를 수정되므로 많은 오버헤드 발생 

### LFU(Least frequently used)
- 참조 횟수가 가장 적은 페이지를 교체
- LRU는 마지막으로 참조된 시점으로부터의 시간을 고려하는 반면, LFU는 참조 빈도를 고려
- 참조 횟수는 적지만 가장 최근에 로드된, 앞으로 많이 활용될 페이지가 교체될 수 있음
- 구현이 어렵고, 막대한 오버헤드가 발생 

### MFU(Most frequently used)
- 여태 많이 사용되었던 페이지는 앞으로는 사용되지 않을 것이라고 가정

### NUR(Not Used Recently, Not Recently Used)
![opt](/sejigner/img/os/page replacement/nur.png)
- 최근에 사용하지 않은 페이지를 교체한다는 점이 LRU와 동일
- 위의 그림을 보면, 3개의 프레임이라고 가정할 때, D가 들어오려면 ABC 중 하나가 빠져야 하는데, 그룹 1이 교체 최우선순위이므로 B가 교체됨
- 같은 그룹 내에서는 최근에 참조된 페이지가 무엇인지를 따지지 않고 무작위로 교체함
- 페이지는 두 개의 비트로 구성
	- 참조 비트 Reference Bit: 페이지가 참조되지 않았을 때 0, 호출되었을 때 1 (주기적으로 모든 참조 비트를 0으로 변경)
	- 변형 비트 Modified Bit, Birty Bit: 페이지 내용에 수정이 없을 경우 0, 있을 경우 1
	- 교체: 참조비트가 변형비트보다 교체 우선순위가 높음
