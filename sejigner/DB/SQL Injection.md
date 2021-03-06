# SQL Injection
> 해커가 조작한 SQL 쿼리문을 통해 데이터베이스에 비정상적인 명령을 실행시키는 공격 방법

## 공격 방법
인증 우회
### 논리적 에러를 이용한 SQL Injection
가장 대중적인 공격 기법

![논리적 에러](/sejigner/img/db/injection/1.png)

- 해커가 입력 값에 대한 검증이 없음을 확인하고, 임의의 SQL 구문을 주입
- OR 1=1이 WHERE 절을 모두 참으로 만들어버리고, --를 넣어 뒤의 구문을 주석으로 만들어 버림
- 간단하게 User 테이블에 있는 모든 정보를 조회할 수 있게 되고, 보통 맨 처음 생성된 것이 관리자 계정이라는 점을 이용하여 관리자 계정을 탈취할 수 있게 되어 더 큰 피해 발생

### Union 명령어를 이용한 SQL Injection

유니온 키워드는 두 개의 쿼리문에 대한 결과를 통합해서 하나의 테이블로 보여줌

![Union](/sejigner/img/db/injection/2.png)

- 위 예시에서는 입력값을 title 과 contents 컬럼의 데이터랑 비교한 뒤 비슷한 글자가 있는 게시글을 출력
- 유니온 하는 두 테이블의 컬럼 수가 같고, 데이터 형이 같으면 원하는 쿼리문 실행
- 현재 인젝션 한 구문은 사용자의 id와 passwd를 요청하는 쿼리문?(컬럼 수 일치)
- 입력 값에 대한 검증이 없기 때문에 발생

### Blind SQL Injection
- Boolean based SQL

데이터베이스로부터 특정한 값이나 데이터를 전달받지 않고, 단순히 참과 거짓의 정보만 알 수 있을 때 사용

로그인 폼에 SQL Injection이 가능하다고 가정 했을 때, 서버가 응답하는 로그인 성공과 로그인 실패 메시지를 이용하여, DB의 테이블 정보 등을 추출
![Boolean](/sejigner/img/db/injection/3.png)

	- limit 키워드를 통해 하나의 테이블만 조회하고, SUBSTR 함수로 첫 글자만, 그리고 마지막으로 ASCII 를 통해서 ascii 값으로 변환
	- 테이블 명이 Users 라면 ‘U’ 자가 ascii 값으로 조회가 될 것이고, 뒤의 100 이라는 숫자 값과 비교
	- 거짓이면 로그인 실패가 될 것이고, 참이 될 때까지 뒤의 100이라는 숫자를 변경해 가면서 비교
	- 이 프로세스를 자동화 스크립트를 통하여 단기간 내에 테이블 명을 알아 낼 수 있음

- Time based SQL
Time Based SQL Injection 도 마찬가지로 서버로부터 특정한 응답 대신에 참 혹은 거짓의 응답을 통해서 데이터베이스의 정보를 유추하는 기법

![Time](/sejigner/img/db/injection/4.png)
	- 데이터베이스의 길이를 알아내는 방법
	- LENGTH 함수는 문자열의 길이를 반환하고, DATABASE 함수는 데이터베이스의 이름을 반환
	- LENGTH(DATABASE()) = 1 가 참이면 SLEEP(2) 가 동작하고, 거짓이면 동작하지 않습니다. 이를 통해서 숫자 1 부분을 조작하여 데이터베이스의 길이를 알아 냄
	- SLEEP 이라는 단어가 치환 처리 되어있다면, 또 다른 방법으로 BENCHMARK 나 WAIT 함수를 사용. BENCHMARK 는 BENCHMARK(1000000,AES_ENCRYPT('hello','goodbye')); 의 형태로 사용이 가능

- 저장된 절차 SQL Injection
	- 일련의 쿼리를 모아 하나의 함수처럼 사용하는 것. 
	- MS-SQL 에 있는 xp_cmdshell로 윈도우 명령어를 사용
	- 공격자가 시스템 권한을 획득 해야 하므로 공격 난이도가 높으나 공격에 성공하면, 서버에 직접적인 피해를 입힐 수 있음

- 대량 SQL Injection
	- SQL Injection 과 달리 한번의 공격으로 다량의 데이터베이스가 조작되어 큰 피해를 입히는 것
	- MS-SQL을 사용하는 ASP 기반 웹 애플리케이션에서 많이 사용
	- 쿼리문은 HEX 인코딩 방식으로 인코딩 하여 공격
	- 데이터베이스 값을 변조하여 데이터베이스에 악성스크립트를 삽입
	- 사용자들이 변조된 사이트에 접속 시 좀비PC로 감염되고 Ddos 공격에 사용됨

## 방어 방법

### 입력 값 검증

- 서버 단에서 블랙리스트 기반으로 검증하게 되면 수많은 차단리스트를 등록해야 함
- 따라서 화이트리스트 기반으로 검증해야 함
- 공백으로 치환하는 방법도 많이 쓰이는데, 이 역시 취약
- 공격자가  SESELECTLECT 라고 입력 시 중간의  SELECT가 공백으로 치환이 되면  SELECT  키워드  완성 
- 공백 대신 공격 키워드와는 의미 없는 단어로 치환되어야 함

> 화이티리스트란 '안전'이 증명된 것만을 허용하는 것으로 '악의성'이 입증된 것을 차단하는 블랙리스트 보안과 상반되는 보안 방식  
 > 출처: [https://sugerent.tistory.com/135](https://sugerent.tistory.com/135) [MISTERY]

### Prepared Statement 구문사용
- Prepared Statement 구문을 사용하게 되면, 사용자의 입력 값이 데이터베이스의 파라미터로 들어가기 전에 DBMS가 미리 컴파일 하여 실행하지 않고 대기
- 사용자의 입력 값을 문자열로 인식하게 하여 공격 쿼리가 들어간다고 하더라도, 사용자의 입력은 이미 의미 없는 단순 문자열 이기 때문에 전체 쿼리문이 공격자의 의도대로 작동 X

### Error Message 노출 금지
- 공격하려면 SQL Injection 데이터베이스의 정보(테이블명, 컬럼명 등)가 필요
- 데이터베이스 에러 발생 시 따로 처리를 해주지 않았다면, 에러가 발생한 쿼리문과 함께 에러에 관한 내용이 반환됨
- 여기서 테이블명 및 컬럼명 그리고 쿼리문이 노출 
- 적절한 에러 처리 필요
  
### 웹 방화벽 사용
- 웹 공격 방어에 특화되어있는 웹 방화벽을 사용 
- 웹 방화벽은 소프트웨어 형, 하드웨어 형, 프록시 형으로 분류 
- 소프트웨어 형: 서버 내에 직접 설치 
- 하드웨어 형: 네트워크 상에서 서버 앞 단에 직접 하드웨어 장비로 구성 
- 프록시 형: DNS 서버 주소를 웹 방화벽으로 바꾸고 서버로 가는 트래픽이 웹 방화벽을 먼저 거치게 함
