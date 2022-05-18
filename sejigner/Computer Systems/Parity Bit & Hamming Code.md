# Parity Bit & Hamming Code
## Parity Bit 
- 정보를 전달하는 과정에서 오류가 발생했는지 검사하기 위해 비트의 맨 끝에 추가되는 비트 

	![parity bit](/sejigner/img/systems_1.4/parityBitHammingCode/1.png)
- 분류 : 홀수 패리티 비트(odd)와 짝수 패리티(even) 비트로 구분된다.
	- 짝수 패리티는 0, 홀수 패리티는 1이 붙게 된다.
	- 홀수 패리티 방식 : 해당 범위의 비트에 들어 있는 1의 개수를 홀수로 만들어주는 패리티 비트
	- 짝수 패리티 방식 : 해당 범위의 비트에 들어있는 1의 개수를 짝수로 만들어주는 패리티 비트
	- 오류의 발생 여부를 알려주지만, 오류가 난 부분을 정정할 수는 없다.

	![parity bit type](/sejigner/img/systems_1.4/parityBitHammingCode/2.png)

|||||||||**패리티**|
|--|--|--|--|--|--|--|--|--|
|홀수 패리티|0|0|0|0|0|0|0|**1**|
|짝수 패리티|0|0|0|0|0|0|0|**0**|

|||||||||**패리티**|
|--|--|--|--|--|--|--|--|--|
|홀수 패리티|1|0|0|0|0|0|0|**0**|
|짝수 패리티|1|0|0|0|0|0|0|**1**|

||||오류|||||**패리티**| 오류 |
|--|--|--|--|--|--|--|--|--|--|
|홀수 패리티|1|1|0|1|1|1|1|**0**|1이 짝수개이므로 오류 검출|
|짝수 패리티|1|1|0|1|1|1|1|**1**|1이 홀수개이므로 오류 검출|

||||오류|오류||||**패리티**| 오류 |
|--|--|--|--|--|--|--|--|--|--|
|홀수 패리티|1|1|0|0|1|1|1|**0**|1이 홀수개이므로 오류 검출 x|
|짝수 패리티|1|1|0|0|1|1|1|**1**|1이 짝수개이므로 오류 검출 x|

오류의 개수가 짝수이면 오류를 검출하지 못하는 문제 -> 병렬 패리티와 해밍코드 도입

## 병렬 패리티 블록합 검사(Block Sum Check)
- 에러를 검출하기 위한 세로 중복 검사
- 예시 데이터 : ABC, 아스키코드 방식 (A : 65, B : 66, C : 67)

	![block sum check](/sejigner/img/systems_1.4/parityBitHammingCode/5.png)

	![block sum check](/sejigner/img/systems_1.4/parityBitHammingCode/6.png)

	![block sum check](/sejigner/img/systems_1.4/parityBitHammingCode/7.png)
	
- 여전히 오류를 발견할 수 없는 경우에는 Hamming Code 방식이 필요하다. 
	
## Hamming Code 
- 에러를 검출하고 수정하는 코드
- 패리티 비트의 개수

	![hamming code](/sejigner/img/systems_1.4/parityBitHammingCode/3.png)

- 홀수 패리티 방식, 전송 데이터가 십진수 14라고 가정하면, 이진수로는 1110이 된다. 공식을 적용하면, 2^3 >= 3 + 4 + 1 이므로, 패리티 비트 개수가 3으로 결정된다.
- 패리티 비트는 2^n(n = 0, 1, 2, 3, ...)번째 비트에 들어간다.

	![hamming code](/sejigner/img/systems_1.4/parityBitHammingCode/4.png)
	


## Reference

https://devuna.tistory.com/31

https://milkye.tistory.com/130

https://youtu.be/NtwMSk2vVIA

https://youtu.be/FJ85Zd3QIKY

> Written with [StackEdit](https://stackedit.io/).
