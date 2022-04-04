﻿
# Process Address Space
![process address space](/sejigner/img/os/address/1.png)

메모리는 구역을 나눠 데이터를 최대한 공유하도록 하여 메모리 사용량을 줄임
## Code 영역
- 프로그램의 코드가 저장되는 공간 Text 영역이라고도 불림
- 함수, 제어문, 상수 등 함수에 대한 기계어 코드가 저장됨
- 컴파일될 때 한번 저장되고 나면 수정될 수 없음 (Read-only)

## Data 영역
- 프로그램이 돌아가는 동안 항상 접근이 가능한 변수들이 저장됨
- 초기값이 있는 전역 변수, 배열, 정적 변수가 해당
- 전역변수를 참조한 코드는 컴파일 과정에서 Data 영역의 주소값을 가르킴
- 프로그램 시작과 함께 할당되어 프로그램이 종료되면 소멸
- 실행 중도에 전역 변수가 변경될 수 있도록 Read-Write 형태

## BSS (Block stated symbol)
- 초기값이 없는 전역변수, 배열, 전역 변수가 저장되는 영역
- 초기화되지 않았으므로 영역만 지정해주면 됨 -> 프로그램의 실행 코드 사이즈에 영향 X

## Stack
- 메서드 호출에 필요한 지역 변수와 매개 변수가 저장됨
- 함수가 호출되면 생성되고, 호출이 끝나면 소멸
- Primitive 데이터가 값과 함께 할당됨
- Heap 영역에 생성된 오브젝트 타입의 데이터 참조 값이 할당
- LIFO, 후입선출
- 함수의 뒷부분부터 변수의 주소가 매겨짐(메모리가 얼만큼 사용될지 미리 예측이 불가능하기 때문) -> 높은 주소에서 낮은 주소 방향으로 할당
- 컴파일 타임에 크기가 결정되어 stack 영역이 초과되면 stack overflow가 발생

## Heap
- 런타임에 크기가 결정 됨(동적)
- 사용자가 임의로 할당하고 해제 가능
- 원시형이 아닌 참조형의 데이터 값이 저장 
- 스택과 반대로 낮은 주소에서 높은 주소 방향으로 할당
- Stack과 같은 메모리 공간을 공유. 영역을 침범하는 주체에 따라 Heap Overflow, Stack Overflow로 구분

> Written with [StackEdit](https://stackedit.io/).