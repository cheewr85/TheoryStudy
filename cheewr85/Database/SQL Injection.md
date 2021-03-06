## SQL Injection
- 보안상의 취약점을 이용하여, 임의의 SQL문을 주입하고 실행되게 하여 DB가 비정상적인 동작을 하도록 조작하는 행위임

- 이용자의 입력값이 SQL 구문의 일부로 사용될 경우, 해커에 의해 조작된 SQL 구문이 데이터베이스에 그대로 전달되어 비정상적인 DB 명령을 실행시키는 공격 기법임

![one](/cheewr85/img/Database/five.png)

-------

## SQL Injection 공격 목적 및 영향

### 인증 우회
- SQL Injection 공격의 대표적인 경우로, 로그인 폼을 대상으로 공격을 수행함, 정상적인 계정 정보 없이도 로그인을 우회하여 인증을 획득할 수 있음

### DB 데이터 조작 및 유출
- 조작된 쿼리가 실행되도록 하여, 기업의 개인정보나 기밀정보에 접근하여 데이터를 획득할 수 있음, 데이터 값을 변경하거나 심지어 테이블을 지워버릴 수도 있음

### 시스템 명령어 실행
- 확장 프로시저를 호출하여 원격으로 시스템 명령어를 수행할 수 있도록 함, 이렇게 시스템 명령어를 실행할 수 있다면 해당 서버의 모든 자원에 접근하고 데이터를 유출 삭제 할 수 있음

----------

## SQL Injection 원리와 유형
- SQL Injection은 SQL 쿼리문에 기반하여 공격을 수행함, 공격에 이용되는 쿼리문은 문법적으로는 지극히 정상적인 SQL 구문임, 다만 실행되지 말아야 할 쿼리문이 실행되어 공격에 이용되는 것임

- SQL Injection은 최소 다음의 조건을 충족해야함

   - 웹 애플리케이션이 DB와 연동하고 있음

   - 외부 입력값이 DB 쿼리문으로 사용됨

----------

## SQL Injection 공격 유형과 공격 기법

### 일반적인 SQL Injection
- 쿼리 조건 무력화(where 구문 우회) : 해커가 이 Where 조건이 무조건 참이 되도록 쿼리를 조작하여 Where 조건을 우회하게 만듬

- 고의적 에러 유발후 정보 획득 : 의도적으로 SQL 구문 에러를 유발하여 웹 애플리케이션이 내뱉은 오류 정보에 기반하여 유용한 정보를 알아차림, 이는 쿼리 수행 중 오류가 발생하면 DB오류를 그대로 브라우저에 출력함, 이 오류 정보를 통해 DB의 스키마 정보나 데이터가 유출될 수 있음

- 시스템 명령어 실행 : MS SQL Server의 경우 시스템 명령을 실행할 수 있는 확장 프로시저를 제공함, 이 시스템 명령을 악용해서 어떠한 시스템 명령도 활용해서 혼란을 줄 수 있음

### Blind SQL Injection 
- 위의 경우가 정말 일반적인 경우라면 쿼리 결과의 참/거짓으로부터 DB 값을 유출해 내는 기법이 Blind SQL Injection임

- 이를 위해서 쿼리 결과에 대해 참/거짓을 반환하는 요소를 찾아서 악용할 수 있음

- Boolean-based Blind 공격 : 예를 들어 게시판 검색 기능을 제공할 때, 이를 참/거짓의 반환을 테스트 해 볼 수 있음, 이 참/거짓을 반환하는 요소를 사용해서 AND 조건으로 해커가 알고 싶은 쿼리 조건을 삽입해서 그 결과로부터 정보 유출이 가능해짐, 이렇게 AND 조건에 논리식을 대입하여 참/거짓 여부를 알아내는 방식을 Boolean-based Blind 공격이라고 함

- Time-based Blind 공격 : 시간을 지연시키는 쿼리를 주입하여 응답 시간의 차이로 참/거짓 여부를 판별할 수 있음, 쿼리 지연을 유도해 응답 시간에 걸리는 시간으로 참/거짓을 판별하게 함으로써 DB의 유용한 정보를 캐낼 수 있음

- Blind SQL Injection은 쿼리 결과가 참일때와 거짓일때의 서버의 반응 만으로 데이터를 얻어낼 수 있는 SQL Injection 공격 기법임

- 이 기법은 많은 조건에 대한 비교과정을 거쳐야 의미있는 정보를 얻을 수 있기 때문에, 거의 모든 경우 자동화 툴을 사용해서 공격이 진행됨

------

## SQL Injection 대응 방안

### 웹 방화벽(WAF) 도입
- 웹 방화벽은 HTTP/HTTPS 응용 계층의 패킷 내용을 기준으로 패킷의 보안성을 평가하고 룰(Rule)에 따라 제어함, SQL Injection의 룰(Rule)을 설정하여 공격에 대비할 수 있음

- 물리적 웹 방화벽 : 기업의 규모가 어느정도 되거나 보안이 중시되는 환경 또는 예산이 충분한 환경에서는 물리적인 전용 WAF 사용을 권장함

- 논리적 웹 방화벽(공개 웹 방화벽) : 전용 웹 방화벽 장비를 도입할 여럭이 되지 않는다면, 공개 웹 방화벽을 고려할 만함, 논리적인 구성으로 웹 방화벽 역할을 수행함

### 시큐어 코딩
- 입력값 유효성 검사 : 모든 외부 입력값은 신뢰하지 말라, 즉 외부에서 들어오는 모든 입력값은 모두 의심의 대상임, 직접 타이핑 값말고도 프록시 툴을 이용하여 중간에 값을 변조할 수 있는 외부값도 포함, 이 입력값의 유효성을 검사하는게 일반적인 대응 전략임

    - 블랙 리스트 방식 : SQL 쿼리의 구조를 변경시키는 문자나 키워드를 제한하는 방식임, 블랙리스트로 미리 정의하여 해당 문자를 공백 등으로 치환하는 방식으로 방어함, DBMS 종류에 따라 쿼리의 구조를 변경시키거나 쿼리문의 일부로 사용되는 문자 필터링

    - 화이트 리스트 방식 : 허용된 문자를 제외하고는 모두 금지하는 방식, 지정된 문자만을 허용하기 때문에 웹 애플리케이션의 기능에 따라 화이트리스트를 다르게 유지해야 할 필요가 생김, 정규식을 이용해서 범주화,패턴화 시킴

- 동적 쿼리 사용 제한 

    - 동적 쿼리 금지 : 현실적으로 동적 쿼리를 사용하지 않을 수 없지만 대응 방안 중 하나임

    - 매개변수화된 쿼리(구조화된 쿼리) 사용 : 동적 쿼리를 정적 쿼리처럼 사용하는 기법임, 쿼리 구문에서 외부 입력값이 SQL 구문의 구조를 변경하지 못하도록 정적구조로 처리하는 방식임

- 오류 메시지 출력 제한

    - DB 오류 출력 제한 : DB 오류가 적나라하게 이용자에게 노출되지 않도록 커스텀 오류 페이지를 제공함

    - 추상화된 안내 메시지 : 너무 자세한 안내 메시지도 주의할 필요가 있음, 추상적인 메시지가 더 나을 때가 있음

- DB 보안

    - DB 계정 분리 : 관리자가 사용하는 DB 계정과 웹애플리케이션이 접근하는 DB 계정은 반드시 분리되어야 함

    - DB 계정 권한 제한 : 웹 애플리케이션이 제공하는 기능만 수행가능하도록 권한을 제한함 즉, DB에 액세서하는 전용 계정을 생성하고 이 계정에는 최소 권한 원칙에 입각하여 꼭 필요한 권하만 할당함

    - 기본/확장 저장 프로시저 제거 : 데이터베이스가 설치될 때 기본적으로 포함된 프로시저들을 꼭 필요한 경우가 아니라면 제거하는 것이 좋음

### 취약점 점검과 모니터링
- 지속적 취약점 점검 : SQL Injection 취약점을 정기적으로 점검해야함

- 로깅과 모니터링 : 유달리 500 오류가 많이 발생하거나 동일한 IP에서 동일한 페이지를 반복적으로 호출할 경우 주의 깊게 살펴야함

---------

[참고자료](https://m.mkexdev.net/427)