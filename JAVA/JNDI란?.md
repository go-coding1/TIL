## JNDI란?

 회사에서 개발환경을 세팅하다가 JNDI를 설정할 일이 있었다. 처음에는 JNDI가 뭐지? 생각하고 있다가 이것 저것 알아보니 매우 중요한 기술인것을 알고 내 나름대로 정리해본다.

> **JNDI**(Java Naming and Directory Interface)는 디렉터리 서비스에서 제공하는 데이터 및 객체를 발견(discover)하고 참고(lookup) 하기 위한 자바 API다.
>
> JNDI는 일반적으로 다음의 용도로 쓰인다:
>
> * 자바 애플리케이션을 외부 디렉터리 서비스에 연결 (예: 주소 데이터베이스 또는 LDAP 서버)
> * 자바 애플릿이 호스팅 웹 컨테이너가 제공하는 구성 정보를 참고
>
> 출처 : [위키백과](https://ko.wikipedia.org/wiki/JNDI)

간단히 요약하자면 우리가 연결하고 싶은 데이터베이스의  DB Pool을 미리 Naming 시켜주는 방법 중 하나이다. 우리가 저장해놓은 WAS 의 데이터베이스 정보에 JNDI를 설정해 놓으면 웹 애플리케이션은 JNDI만 호출하면 간단해진다.

그럼 왜 사용하게 되는걸까? 우리는 보통 JDBC를 설정해서 개발을 한다. 하지만 웹 애플리케이션을 운영서버로 만들경우 얘기가 달라진다. 그 이유는

1. 개발을 한 사람과 실제 서비스를 운영하는 Admin은 다른 경우가 많기 때문에 소스 레벨에서 설정되어 있는 것보다 WAS에서 설정되어 있는 것을 선호한다.
2. 또한 WAS에 여러 개의 웹 애플리케이션을 올려서 사용하기 때문에 WAS에서 한 번에 설정해 주는 것이 자원낭비를 줄일 수 있습니다.
3. 또한 장애가 나거나 성능이 정상적이지 못하면 다른 한 서버가 대신 일을 해줄 수 있습니다.

정리하자면 **운영, 관리, 최적화 문제 대처**에 다양한 이점이 있기 때문에 JNDI를 사용한다.

### 사용 예시

설정해줘야 할 파일은 `web.xml`과 `server.xml`이다.

```xml
....web.xml
<resource-ref>
	<description>Resource</description>
    <res-ref-name>jdbc/EmployeeDB</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
</resource-ref>
```

```xml
...server.xml
<Resource name="jdbc/EmployeeDB"
          auth="Container"
          type="javax.sql.DataSource"
          username="dbusername"
          password="dbpassword"
          driverClassName="driverName"
          url="jdbc:oracle:thin..."
          maxActive="8"
          maxIdle="4"/>
```

어떤 WAS를 쓰느냐에 따라 설정해줘야 할 파일과 규칙이 다 다르겠지만 기본적으로 위와 같이 설정하는 것이 가장 대표적이다 위 처럼 설정해 놓고 실제 DB와의 연결하는 코드는

```java
InitialContext initCtx = new InitialContext();
DataSource ds = (DataSource) initCtx.lookup("java:comp/env");
```

으로 접근할 수 있다.

