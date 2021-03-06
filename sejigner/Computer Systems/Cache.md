
# Cache
- 주기억장치에서 자주 사용하는 프로그램과 데이터를 저장해두어서 속도를 빠르게 하는 메모리
- 캐시는 주기억장치보다 크기가 작지만(수십 Byte~ 수백 KByte) 거의 CPU와 맞먹을 정도의 속도를 갖는다. 메모리 계층 구조에서 가장 빠른 소자이다.
- 캐시 용량 : 용량이 커질수록 히트율이 높아지지만, 용량이 커지면 정보 인출을 위한 회로가 복잡해져서 데이터 접근 시간이 길어진다.
- 속도가 빠른 대신 용량이 작은 레지스터와 상대적으로 속도는 느리지만 용량이 큰 주기억장치 사이에서 발생하는 병목현상을 줄이기 위해 사용되는 범용 메모리이다.
	- 병목 현상을 줄이기 위해 CPU가 필요로 하는 데이터는 무엇인지 예측이 가능해야 한다.
	- 즉, 캐시의 성능은 CPU가 이후에 참조할 데이터 주소값을 얼마나 잘 예측해서 미리 가지고 있느냐에 달려있다.
![cache](/sejigner/img/systems_1.4/cache/1.jpg)
## Mapping(매핑, 사상)
캐시 메모리와 주기억장치 간 정보를 옮기는 것
### 매핑의 종류
- 직접 매핑(direct mapping)
	![cache](/sejigner/img/systems_1.4/cache/2.jpg)
	- 주기억장치의 블록들이 한 개의 캐시 라인으로만 사상될 수 있는 매핑 방법
	- 붉은색 부분은 블럭의 크기를 의미한다. 00부터 11까지 총 4개이므로 블록의 크기는 4이다.
	- 파란색 부분은 같은 캐시의 라인 내에서 데이터를 구분해준다.
	- 캐시에 저장된 데이터가 메인 메모리에서와 동일반 배열을 갖도록 매핑한다. 
	- 간단하고 비용이 적지만 적중률이 낮다.
- 연관 매핑(associative mapping)
![cache](/sejigner/img/systems_1.4/cache/3.jpg)
	- 직접 매핑의 단점을 보완한 방식으로 모든 태그를 병렬로 검사하기 때문에 복잡하고 비용이 많이 들어 거의 사용되지 않는다.
	- 캐시에 저장된 데이터들은 메인 메모리의 순서와 아무 관련 없다.
- 집합, 연관 매핑(set-associative mapping)
![cache](/sejigner/img/systems_1.4/cache/4.jpg)
	- 직접 매핑과 연관 매핑을 절충하여, 메모리 블록을 정해진 블록의 집합 내 어디든 매핑한다.
	- Set를 먼저 설정한 후 그 중 어느 블록에 넣을지 결정한다.
	- 직접 매핑의 단순한 회로와 연관 매핑의 높은 적중률 모두를 취하기 위해 택하는 방식이다.
	- 각각의 라인은 하나의 세트에 속한다.
	- 세트 번호를 통해 영역을 탐색하여 병렬 탐색을 줄인다.
	- 세트 안의 라인 수에 따라 n-way 연관 매핑이라고 한다. 위의 예는 2-way 집합 연관 매핑이다.
## Locality
- 데이터가 시간적, 공간적으로 가까워 접근이 효율적임을 의미하는 것이다.
	- 시간적 지역성 : 특정 데이터가 한번 접근되었을 경우, 가까운 미래에 다시 한번 접근될 가능성이 높다고 추측하고 캐시에 매핑된다.
	- 공간적 지역성 : 특정 데이터와 주소가 순서대로 접근되었을 경우에 주소가 가까운 다른 데이터도 접근될 가능성이 높다고 추측하고 캐시에 매핑한다. CPU 캐시나 디스크의 캐시는 한 메모리 주소에 접근할 때 그 주소 뿐만 아니라 블록 전체를 캐시에 매핑한다. 
- 캐시는 기억장치의 정보를 균일하게 접근하지 않고, 어느 한 순간에 특정 부분을 집중적으로 참조하는 특징을 갖는다.
- 캐시의 효율적인 동작을 위해서는 캐시의 적중률(Hit-Rate)를 극대화시켜야 한다.

## Reference
http://itnovice1.blogspot.com/2019/09/cache.html
> Written with [StackEdit](https://stackedit.io/).
