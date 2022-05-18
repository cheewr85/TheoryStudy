# Android Context

- 애플리케이션의 현재 상태를 나타냄

- 리소스, 데이터베이스, sharedPreference에 접근하기 위해 사용

- 액티비티와 애플리케이션은 Context 클래스를 확장한 클래스

- Application Context, Activity Context 분류

## Application Context

- 애플리케이션 컨택스트는 싱글턴 인스턴스

- 앱 라이프사이클에 연결. 컨택스트가 종료된 이후 컨택스트가 필요한 작업이나 액티비티 스코프를 벗어난 컨택스트가 필요할 경우 사용

## Activity Context

- Activity에서 유효한 컨택스트

- 액티비티 라이프사이클에 연결 -> 액티비티와 함께 소멸해야 하는 경우에 사용

![](https://blog.kakaocdn.net/dn/WVBRX/btqWxfzYqKQ/IzwkE5gnxovo8rKrv7z02K/img.jpg)

- App Context는 MyApplication, MainActivity1, MainActivity2 모두에서 사용 가능

- MainActivity1의 컨택스트는 MainActivity1에서만 사용 가능

- 가능한 한 가까운 컨택스트를 사용하는 것이 바람직
  
  - 액티비티 - 액티비티 컨택스트
  
  - 앱 - 앱 컨택스트
  
  - 싱글톤 - 애플리케이션 컨택스트

- c.f. 싱글톤 : 전역 변수를 사용하지 않고 객체를 하나만 생성, 생성된 객체를 어디서든 참조할 수 있도록 하는 패턴

```kotlin
public class Singleton {

    private static Singleton instance = new Singleton();
    
    private Singleton() {
        // 생성자는 외부에서 호출못하게 private 으로 지정해야 한다.
    }

    public static Singleton getInstance() {
        return instance;
    }

    public void say() {
        System.out.println("hi, there");
    }
}
```


