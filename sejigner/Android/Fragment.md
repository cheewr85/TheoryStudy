# Fragment

- 액티비티 또는 다른 프래그먼트가 호스트가 되어 그에 따라 종속적으로 존재

- 프래그먼트의 뷰 계층 구조 역시 호스트 뷰 계층 구조의 일부가 되거나 그것에 연결됨

- 자체 레이아웃 정의 및 보유

- 자체 수명 주기

- 자체 입력 이벤트 처리

- BottomNavigationView나 ViewPager2와 같은 Android Jetpack 라이브러리의 일부와 호환



## Modularity 모듈성

**Modularity & Reusability**

- 액티비티가 앱의 UI 주위 전역 요소를 배치하기에 적합한 반면 프래그먼트는 단일 화면이나 화면 일부 UI를 정의하고 관리하는 데 적합
  ![](C:\Users\sejig\AppData\Roaming\marktext\images\2022-01-10-18-37-49-image.png)
  액티비티 Navigation UI + 프래그먼트 리스트 레이아웃

- 액티비티 수명주기가 STARTED일 때 부터 프래그먼트 추가, 교체, 삭제 가능

- 백 스택에 두어 관리 

- 프래그먼트 자체 UI에 필요한 로직만 해당 프래그먼트 클래스에 작성

- 프래그먼트 관리는 종속된 액티비티나 프래그먼트에서 담당해야 함

## 액티비티에 프래그먼트 추가하기

1. 액티비티 레이아웃 파일에 프래그먼트 정의

2. 액티비티 레이아웃 파일에 프래그먼트 container 정의 후 액티비티에 프래그먼트 동적으로 추가
- 두 가지 방식 모두 프래그먼트가 표시될 위치를 정의하는 FragmentContainerView를 추가해야 함

- FrameLayout보다는 FragmentContainerView 사용이 권장됨



## FragmentManager

- 프래그먼트 상 작업을 추가, 삭제, 교체, 백 스택에 추가 등의 작업 실행

- Jetpack Navigation 라이브러리를 사용하는 경우 FragmentManager를 통해 직접 상호작용하지 않지만 그 외의 경우엔 이를 통해 프래그먼트 관리

### Fragment 액세스

- 액티비티에서 액세스 : getSupportFragmentManager()

- 프래그먼트에서 액세스 : getChildFragmentManager() & getParentFragmentManager()
  호스트 프래그먼트 = 부모 프래그먼트 <-> 자식 프래그먼트

![](C:\Users\sejig\AppData\Roaming\marktext\images\2022-01-10-18-57-10-image.png)

![](C:\Users\sejig\AppData\Roaming\marktext\images\2022-01-10-19-50-51-image.png)
