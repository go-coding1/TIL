# Java or JSP JNDI 사용 정리

### Connection 관리

기존 JDBC 프로그램 구현으로 DBMS와 연동 작업을 할때는 웹 클라이언트로부터 요청이 있을 때마다 DB서버에 연결하기 위해 Connection 객체를 얻어내야 했습니다. 이러한 기존의 JDBC 프로그래밍 작업에는 다음과 같은 문제가 있습니다.

* DB프로그램에서 트랜잭션 처리와 Connection 관리는 시스템의 성능과 안전성에 큰 영향을 미친다.
* Connection 과정은 일정 시간이 필요한 부담과 작업이다.
* 불피요한 연결에 의한 서버 자원의 낭비를 발생한다.

### Connection Pool 

커넥션 풀 개념은 Connection 객체를 프로그램이 실행될 때마다 생성하는 것이 아니라, 웹 애플리케이션이 서비스되기 전에 웹서버에서 미리 생성하여 준비한 다음, 필요할 때 준비된 Connnection을 가져다 사용함으로써 JDBC 프로그래밍의 문제점들을 개선한 기술입니다.

### DataSource

커넥션풀에는 여러개의 Connection 객체가 생성되어 운용되는데, 이를 직접 웹 애플리케이션에서 다루기 힘들기 때문에 DataSource라는 개념을 도입하여 사용합니다.

**DataSource에 대해 정의하자면 아래와 같다**

* 커넥션 풀의 Connection을 관리하기 위한 객체이다.
* JNDI Server를 통해서 이용된다.
* DataSource 객체를 통해서 필요한 Connection을 획득, 반납 등의 작업을 한다.

**DataSource를 이용하려면 다음의 절차를 따릅니다.**

1. JNDI Server에서 lookup()메소드를 통해 DataSource 객체를 획득한다.
2. DataSource 객체의 getConnection()메소드를 통해서 Connection Pool에서 Free 상태의 Connection 객체를 획득한다.
3. Connection 객체를 통한 DBMS 작업을 수행한다.
4. 모든 작업이 끝나면 DataSource 객체를 통해서 Connection Pool에 Connection을 반납한다.

### JNDI

JNDI(Java Naming and Directory Interface)는 디렉터리 서비스에서 제공하는 데이터 및 객체를 발견하고 참고하기 위한 자바 API다.

**Naming & Directory 서비스**

Naming & Directory 서비스는 실제 어떤 자원을 가지고 서비스 한다는 이미가 아니라, 어떤 서버나 애플리케이션에서 분산환경에 서비스하고자 하는 자원을 이 Naming & Directory 서버에 이름값과 실제 자원을 연결하여 등록하면, 해당 자원을 이용하고자 하는 애플리케이션에서 Naming & Directory 서버에 접근하여 이름값만을 가지고 자원을 연결하여 이용할 수 있게 하는 개념입니다.

네이밍 서비스의 대표적인 예로 DNS서버가 이런 기능을 한다. 브라우저창에 URL을 입력하면 브라우저는 DNS서버를 통해 도메인에 해당하는 IP주소를 얻습니다. 그리고 IP 주소를 통해 실제 인터넷 서버에 접속합니다. 결국 DNS 서버는 실제 인터넷 서비스를 수행하는 곳이 아니며, 단지 도메인과 IP 주소만을 연결해 주는 기능을 하는 것입니다. **Naming & Directory 서버 또는 분산환경에서 자원을 연결해주는 기능을 합니다.**



---

### 구현하기

DataSource.java

```java
import java.sql.*;
import javax.sql.*;
import javax.naming.*;

class DataSouceImpl{
    private Connection conn = null;
    public void getConnection(){
        //JNDI 서버 객체 생성
        InitialContext ic = new InitialContext();
        
        //lookup()
        DataSource ds = (DataSource) ic.lookup("java:/comp/env/jdbc/hms");
        
        //getConnection()
        Connection connection = ds.getConnection();
        
        this.conn = connection;
    }
    
    public void doSQL(){
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery("select * from test");
        
        while(rs.next()){
            out.print(rs.getString("id") + ":" + rs.getString(2));
        }
        
        //반납
        rs.close();
        stmt.close();
        conn.close();
    }
    
}
```

소스 설명

```java
import java.sql.*;	//일반적인 데이터베이스 작업
import javax.sql.*;	//DataSource 객체를 사용하기 위해
import javax.naming.*;	//JNDI 작업을 하기 위해 import 
```

```java
/*
커넥션 풀에 접근하려면 JNDI 서비스를 사용해야 합니다. JNDI는 서버에서 관리하고 있는 리소스에 대한 정보를 알고 있고 특정 리소스를 찾아서 사용할 수 있도록 객체를 반환해주는 역할을 합니다. JNDI 서버역할을 하는 객체를 생성합니다. 리소스가 단순히 로컬에 있을 때는 단순히 InitialContext 객체만 생성하면 됩니다.*/
InitialContext ic = new InitialContext(); 
```

```java
/*
ic.lookup()은 리소스를 찾은 후 리소스를 사용할 수 있도록 객체를 반환해주는 메소드입니다. lookup() 메소드의 인자값으로는 찾으려는 리소스의 등록된 이름을 지정하도록 합니다. 우리가 찾으려는 리소스의 이름은 "jdbc/hms"이고 WAS인 톰갯에서 리소스를 관리하는 가상의 디렉터리는 "java:/comp/env"입니다. 그래서 lookup() 메소드의 최종인자 값은 "java:/comp/env/jdbc/hms"가 됩니다.*/
DataSource ds = (DataSource) ic.lookup("java:/comp/env/jdbc/hms");
```

