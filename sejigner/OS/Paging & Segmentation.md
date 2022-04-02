# Segmentation & Paging
> 페이징과 세그먼테이션은 현대 운영체제에서 혼용되는 가상 메모리 관리 기법이다.

![virtual memory](/sejigner/img/os/paging&segmentation/1.png)
## 가상 메모리 
프로세스가 실제 메모리 크기와 상관없이 메모리를 사용할 수 있도록 가상 메모리 주소를 사용하는 것
- 프로세스의 일부만 메인 메모리에 할당하고, HDD, SSD 등의 보조기억장치의 공간을 활용
- 실제 메모리 RAM의 공간적 제약을 벗어나 큰 공간 활용 가능-> 논리적 연속성 부여
- 물리 메모리 주소 공간을 알 필요가 없음
- MMU(메모리 관리 장치)를 통해 가상 주소를 실제 메모리 주소로 변환
## 세그먼테이션 (Segmentation)
![virtual memory](/sejigner/img/os/paging&segmentation/4.png)
- 프로세스를 다양한 크기의 논리적인 단위 Segment로 분할하고, 가상메모리를 할당하여 물리 주소를 논리 주소로 변환
- 세그먼트의 크기는 프로세스의 논리 단위로서, 가상메모리의 크기를 미리 예측하여 분할해 둘 수 없고, 메모리에 적재할 때 빈 공간을 찾아서 할당
- 매핑을 위해 세그먼트 테이블 필요
	- 세그먼트 테이블(segment table) : 세그먼트 번호(s), 시작 주소(base), 세그먼트 크기(limit)를 엔트리로 갖음
- 내부 단편화 X, 외부 단편화 O

## 페이징 (Paging)
![virtual memory](/sejigner/img/os/paging&segmentation/3.png)
- 세그먼테이션과 가상 메모리를 고정된 크기로 분할하여 관리
- 가상 메모리를 일정한 크기로 나눈 블록 단위 : 페이지
- 페이지와 같은 크기로 물리 메모리를 나눈 블록 단위 : 프레임
- 페이지가 하나의 프레임을 할당받으면, 페이지 테이블을 통해서 page를 frame으로 매핑
	- 페이지 테이블(page table)
 
		![virtual memory](/sejigner/img/os/paging&segmentation/2.png)
		- page에 매핑되는 frame을 찾을 때 참조하는 표
		- 페이지 번호를 의미하는 key(or index)와 page와 매핑된 frame 번호와 기타 플래그 정보를 의미하는 value를 포함
- 프레임을 할당받지 못한 페이지는 외부 저장장치에 같은 크기로 저장되어 관리됨
## 내부단편화 vs 외부 단편화
### 내부 단편화
![virtual memory](/sejigner/img/os/paging&segmentation/internal.png)
- 메모리에 프로세스를 고정된 크기로 분할하여 올렸을 때 활용할 수 없는 공간이 생기는 문제
### 외부 단편화
![virtual memory](/sejigner/img/os/paging&segmentation/external.png)
- 아직 메모리 공간이 남아있지만 공간이 연속적이지 않아서 하나의 프로세스를 할당할 수 없는 문제
