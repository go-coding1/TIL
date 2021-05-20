# JBoss 설치 순서[Windows]

* RedHat 가입 및 JBoss EAP 7.3 zip 파일 다운로드

  [다운로드](https://developers.redhat.com/products/eap/download)

* 압축파일 압축해제

  ```cmd
  unzip jboss-eap-7.3.0.zip
  ```

* 관리자 등록

  JBOSS 설치하면 기본적으로 등록된 관리자가 없음.

  ZIP파일을 이용하여 설치할 경우 add-user.sh를 실행하여 초기 관리자 아이디와 패스워드를 등록해야함.

  `jboss-eap-7.3\bin`디렉터리로 이동한 후 `add-user.bat`파일 실행

```cmd
What type of user do you wish to add?
 a) Management User (mgmt-users.properties)
 b) Application User (application-users.properties)
(a): a

Enter the details of the new user to add.
Using realm 'ManagementRealm' as discovered from the existing property files.
Username : win
Password recommendations are listed below. To modify these restrictions edit the add-user.properties configuration file.
 - The password should be different from the username
 - The password should not be one of the following restricted values {root, admin, administrator}
 - The password should contain at least 8 characters, 1 alphabetic character(s), 1 digit(s), 1 non-alphanumeric symbol(s)
Password :
Re-enter Password :
What groups do you want this user to belong to? (Please enter a comma separated list, or leave blank for none)[  ]:
About to add user 'win' for realm 'ManagementRealm'
Is this correct yes/no? yes
Added user 'win' to file 'C:\JBOSS73\jboss-eap-7.3\standalone\configuration\mgmt-users.properties'
Added user 'win' to file 'C:\JBOSS73\jboss-eap-7.3\domain\configuration\mgmt-users.properties'
Added user 'win' with groups  to file 'C:\JBOSS73\jboss-eap-7.3\standalone\configuration\mgmt-groups.properties'
Added user 'win' with groups  to file 'C:\JBOSS73\jboss-eap-7.3\domain\configuration\mgmt-groups.properties'
Is this new user going to be used for one AS process to connect to another AS process?
e.g. for a slave host controller connecting to the master or for a Remoting connection for server to server EJB calls.
yes/no? yes
To represent the user add the following to the server-identities definition <secret value="d2luMTIzNCE=" />
```

`username`과 `password`를 입력하고 여러가지 계정 설정에 대한 물음이 나왔다. 다 yes를 입력해 주면 자동으로 설정파일에 내가 만든 계정 정보를 추가해 주는듯 하다.

# Standalone 모드 서버 실행함

> * standalone type
>   * 시스템에 독자적으로 프로세스가 구동되어 서비스스를 제공하는 데몬을 말한다. 서비스를 하려면 해당 서버가 메모리에 미리 올라와 잇어야 한다.
>   * 예를 들면, 웹서버, DB서버, 샌드메일 서버등이 있다.
>   * 서버 파일 자체로 완전하게 구동한다는 개념이며, 도커는 도커라는 가상화 소프트웨어의 동무을 받아 구동한다는 차이가 있다.
> * xinet 방식
>   * standalone 방식과 가장 다른점은 해당 서버가 클라이언트가 서비스를 요청할 때 그때 자동으로 올라간다. 관리자가 서버를 올리거나 내리거나 할 필요없고 항상 메모리를 점유하는게 아니므로 편리하고 메모리 사용에 효율적이다.

`jboss-eap-7.3\bin\standalone.bat`파일을 실행해 standalone 모드로 서버를 실행한다.

그러면 서버가 실행이 된다.

## 윈도우에서 CML실행하는 법

`\jboss-eap-7.3\bin`dptj `jboss-cli.bat`을 실행하면

```cmd
You are disconnected at the moment. Type 'connect' to connect to the server or 'help' for the list of supported commands.
[disconnected /] connect
[standalone@localhost:9990 /]
```

위에 처럼 connect 쳐 주면 지금 실행하고 있는 jboss에 연결이 됨

# Datasource 등록

1. [여기](https://repo1.maven.org/maven2/com/oracle/database/)에 접속해 ojdbc6.jar 파일을 다운 받는다.

2. jBoss Oracle JDBC 모듈 추가

   ojdbc6.jar 파일을 `jboss-eap-7.3\modules\system\layers\base\com\oracle\ojdbc6\main`디렉터리에 복사 한후 model.xml 파일을 생성하고 아래 내용을 저장함

   참고로 `\com`까지는 이미 생성이 되어있었고 그 아래 폴더는 내가 직접 만들어줌

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <module xmlns="urn:jboss:module:1.1" name="com.oracle.ojdbc6">
       <resources>
           <resource-root path="ojdbc6.jar"/>
       </resources>
       <dependencies>
           <module name="javax.api" />
           <module name="javax.transaction.api"/>
           <module name="javax.servlet.api"/>
       </dependencies>
   </module>
   ```

3. DataSource 설정

   `jboss-eap-7.3\standalone\configuration\standalone.xml`에 DataSource 정보를 추가해 준다. 기본적으로 H2DataSource가 설정되어 있다.

   ```xml
    <datasources>
                  ...
                   
                   <!-- oracle ojdbc jdbc/h5prd  -->
                   <datasource jta="false" jndi-name="java:/jdbc/h5prd" pool-name="oracleh5prd" enabled="true" use-ccm="false">
                   		<connection-url>jdbc:oracle:thin:@localhost:1521/orcl</connection-url>
                   		<driver>oracle</driver>
                   		<security>
                   			<user-name>설치되어있는 DB username</user-name>
                   			<password>설치되어있는 DB password</password>
                   		</security>
                   		<statement>
                           <prepared-statement-cache-size>32</prepared-statement-cache-size>
                       </statement>
                   </datasource>
                   
                   <!-- oracle ojdbc jdbc/h5prd  -->
                   <datasource jta="false" jndi-name="java:/jdbc/h5prdtx" pool-name="oracleh5prdtx" enabled="true" use-ccm="false">
                   		<connection-url>jdbc:oracle:thin:@localhost:1521/orcl</connection-url>
                   		<driver>oracle</driver>
                   		<security>
                   			<user-name>설치되어있는 DB username</user-name>
                   			<password>설치되어있는 DB password</password>
                   		</security>
                   		<statement>
                           <prepared-statement-cache-size>32</prepared-statement-cache-size>
                       </statement>
                   </datasource>
                      ...
                       
                       <!-- oracle ojdbc driver  -->
                      <driver name="oracle" module="com.oracle">
                       	<driver-class>oracle.jdbc.OracleDriver</driver-class>
                       </driver>
                   </drivers>
               </datasources>
   ```

   그냥 이렇게 하니까 되었다.원래는 `<driver-class></>`가 `<xa-datasource-class></>`로 했는데 에러가 나서 이렇게 설정하였다. 근데 왜 되는건지는 모르겠다. 흠...

   `<datasource>`는 2개 지정해야 하고 오라클 `<driver>`는 1개 로 지정해야 한다. `<datasource>`의 pool-name이 서로 달라야 오류가 나지 않는다.

   참조 링크 (데이터베이스 설정)[https://chanchan-father.tistory.com/240]
