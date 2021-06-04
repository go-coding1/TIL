# Scouter APM

> APM (Application Performance Mornitoring)
>
> LG CNS : 컨설팅, 시스템통합 등 IT 서비스를 공급하는 LG그룹 계열의 정보기술 솔루션 및 아웃소싱 전문기업이다.
>
> 벤더(VENDOR) : IT업체에서의 벤더 개념은 일반적으로 판매인 또는 판매업자를 가리키는 말입니다. 특히 컴퓨터 시스템의 하드웨어나 소프트웨어 제품을 사용자에게 판매하였을 때 그 제품의 브랜드에 대해 책임을 지는 기업으로서 벤더는 제조업체일 수도 있고 판매 회사가 아닐 수도 있습니다. 어느 특정 회사의 제품만으로 시스템을 구축하는 것을 싱글 벤더라 하며 여러 회사 제품을 조합하여 시스템을 구축하는 것을 멀티벤더라고 합니다.
>
>
> 
> XLog차트 : XLog차트는 모든 요청에 대해 하나의 점으로 표현하는 일종의 scatter 차트로서 x축에는 요청이 종료된 시간을, y축에는 (주로)해당 요청의 응답시간을 표현합니다.

Scouter는 LG CNS에서 만들어 공개한 [오픈소스](https://github.com/scouter-project/scouter) APM이다. APM은 애플리케이션 성능을 모니터링/ 통제하는 도구로 2016년 현재 아주 많은 종류의 APM이 존재한다. 대표적인 상용 APM으로 NewRelic과 Dynatrace가 있으며, 국산 툴도 몇 가지 존재한다.

오픈소스 APM인 Scouter는 JVM(WAS, Standalone application)을 사용하는 애플리케이션 및 OS자원에 대한 모니터링 기능을 제공한다.

* 모니터링 대상 (전용 agent)
  * Java Agent : Web Application (on Tomcat, JBoss, Resin ...), Standalone java application
  * Host Agent : Linux, Windows, Unix
* 모니터링 대상 (Telegraf support)
  * Redis, nginX, apache httpd, haproxy, Kafka, MySQL, MongoDB, RabbitMQ, ElasticSearch, Kube, Mesos...
* 모니터링 대상(Zipkin-Scouter storage)
  * zipkin instrumentations (C#, Go, Python, Javascript, PHP...)를 XLog 차트를 통해 디스플레이 합니다.

[설치하기](https://github.com/scouter-project/scouter/blob/master/scouter.document/main/Quick-Start_kr.md)

설치하는 방법이 설명되어 있는 페이지이다. Scouter를 설치하기 위해서는 기본적인 구조를 알아야 한다. Scouter는 agent - server - client로 구성되어있다.

### 모듈

---

**스카우터는 4가지 주요 모듈로 구성된다.**

![scouter4](https://user-images.githubusercontent.com/54675591/120736104-9d18a800-c526-11eb-8503-2e5b1ca47ece.png)

* Agent : **성능 데이터를 수집하여 수집 서버로 전송하는 역할**, 지금은 java / host 용 agent 가 별도로 존재한다. **java용 agent는 JVM이 시작할 때 옵션을 추가하여 적용 가능하고**, 시작 시점부터 server에 각종 데이터를 전달한다. host용 agent는 아무때나 프로세스를 시작하면 해당 장비의 CPU, 메모리등의 데이터를 수집하여 server로 전달한다.
  * Tomcat Agent (Java Agent) : JVM 과 Tomcat WAS 성능 수집
  * Host Agent (OS Agent) : Linux, Windows 및 OSX 성능
  * MariaDB Agent 
* Server (Collector) :**Agent가 전송한 데이터를 저장하고 Client 요청시 Client에게 적절한 데이터를 전송한다.**  server는 scala로 작성되어 있으나 일반 자바 개발자들이 사용하는데 큰 무리가 없다.
* Client (Viewer) : **수집된 데이터를 보기 위한 RCP 기반 Client 프로그램.** client는 이클립스 프레임웍 기반으로 개발되어 있으며, 각 client별로 build가 되어 있기 때문에 사용하는데 그리 어렵지는 않을 것이다.
* Web API (Since @1.8.0) : 성능 카운터, XLog, 프로파일등의 정보를 HTTP 프로토콜을 통해 제공

---

### 사용하기

Scouter는 처음에는 간단하고 쉬우며 간단하게 사용하려면 그냥 기본적인 사용법만 알면 된다. 하지만, scouter를 제대로 사용하려면 아주 많은 기능들이 내장되어 있는 도구이기 때문에 전문가의 손길이 필요할 수도 있다. 그럴 때에는 scouter 사용자 모임에서 정보를 얻을 수 있다.

### Scouter가 제공하는 기능

기본적으로는 WAS에서 벌어지는 대부분의 내용들을 확인 해 볼 수 있다.

* 각 요청의 응답속도 / 프로파일링 정보
* 서버 요청 수 / 응답 수
* 처리 중인 요청 수
* 응답속도의 평균
* JVM 메모리 사용량 / GC시간
* CPU사용량

그리고 프로파일링 정보를 통해서

* 서버간 요청의 흐름
* 각 SQL 쿼리의 수행 시간 / 통계
* API호출 수행 시간
* request hearder정보
* 메소드 호출시 수행 시간

등을 상세하게 볼 수 있다.

이렇게 상세하게 볼 수 있다는 말은 거꾸로 말하면 아주 많은 데이터가 저장된다는 것이다. 따라서 Scouter server로 사용하는 장비는 디스크 용량이 많은 장비를 추천한다.

마지막으로 필자가 Scouter와 관련된 지원 없무를 수행하면서 많이 받은 질문들을 정리하면서 이 글을 마치겠다. 

* Q : 사용할 수 있는 WAS 종류가 무엇이 있나요?
* A :  Tomcat. JBoss, Resin 등이 있나고 깃헙에 나와 있습니다.
* Q : WAS만 사용 가능한가요? 일반 데몬처럼 떠 있는 자바 프로세스는 사용 가능한가요?
* A : 사용 가능해요, 단 추가적인 옵션 설정이 필요합니다.
* Q : 하드는 몇 기가나 있으면 되나요?
* A : 일단 100GB 장비에 설치해 보세요. Scouter는 저장소 하드가 70~80% 가 넘으면 오래된 데이터부터 지운답니다. 기본 설정이 되어 있고, 본인이 직접 Scouter server에 설정해도 됩니다. 
* Q : 성능 저하가 얼마나 되나요?
* A : CPU는 지금 쓰는 것에 2~10% 증가하고, 네트워크는 UDP를 많이 씁니다. TCP도 가끔 쓰고요. 네트워크 상황이 안좋다면 모니터링을 별도로 하셔야 합니다.
* Q : 단점은 없나요?
* A : 지금으로썬 잘 모르겠지만, 문제 있을 때 부를 벤더가 없는것?



### 설치하기

---

**Scouter 설치 전 반드시 기억해야할 사항**

Scouter는 Agent와 Controller Server 그리고 User용 Client 프로그램으로 구성되며, 이들간의 관계를 잘 아는 것이 중요합니다.

* 각 서버에 설치된 Scouter의 Agent들이 성능 데이터를 Collector로 전송한다.
* 사용자는 Client 프로그램을 통해 성능 데이터를 본다.

>* Agent =>=> [성능 데이터] =>=> Collector(Server)
>* Client <=<= [성능 데이터] <=<= Collector(Server)

1. Scouter 다운로드

Scouter 릴리즈 페이지에서 최신버전을 다운로드 합니다.

- [Scouter Release Page](https://github.com/scouter-project/scouter/releases)

- **scouter-all-[version].tar.gz**

- - Scouter Collector와 Agent를 포함하는 압축파일입니다.

- **scouter.client.product-[os].tar.gz**

- - 각 OS별 Client(Viewer) 프로그램입니다.

2. Scouter Server 설치 및 기동

* 적절한 위치에 scouter-all-[version].tar.gz 의 압축을 풀어줍니다.

* Scouter Server를 실행합니다.

  startup.sh 또는 startup.bat 실행하면 아래와 같이 실행이 되고 6100 포트로 Scouter Server가 Listen하고 있는 것을 알 수 있습니다.

  <img src="https://user-images.githubusercontent.com/54675591/120735426-8de52a80-c525-11eb-9d5f-c3bf05e465e1.PNG" alt="scouter1" style="zoom:50%;" />

3. Scouter Client 실행

* scouter.client.product-[os].tar.gz의 압축을 풀고`scouter.exe`를 실행합니다.

* 접속할 Scouter Server의 IP나 도메인을 입력합니다.

* ID와 Password를 입력합니다. 기본(admin / admin)

  <img src="https://user-images.githubusercontent.com/54675591/120735828-3abfa780-c526-11eb-86a1-31d194e56f35.PNG" alt="scouter2" style="zoom: 67%;" />

* Collector에 접속되면 좌측 상단의 Object-View에서 Collector 서버 하나가 있는 것을 확인할 수 있습니다. 이후에 Agent를 실행할때마다 이 Collector 서버 하위에 모니터링 대상이 나타나게 됩니다.

  <img src="https://user-images.githubusercontent.com/54675591/120736025-81ad9d00-c526-11eb-8254-6ca72e477a56.PNG" alt="scouter3" style="zoom:67%;" />

4. Host Agent 실행

* Host Agent 실행 전에 먼저 Collector 접속 설정이 필요합니다.

* 설정 파일에 Collector의 IP와 Port정보를 설정합니다.

  설정 파일 위치 : `[scouter-dir]\scouter\agent.host\conf`

  ```conf
  ### scouter host configruation sample
  net_collector_ip=127.0.0.1
  net_collector_udp=6100
  net_collector_tcp=6100
  ```

* net_collector_ip에는 Collector접속 IP나 도메인을 기입합니다.

* 기본 port 값이 6100이므로, Collector 설치시 port 정보를 변경하지 않았다면 port설정은 하지 않아도 됩니다.

> 위 설정에서 알 수 있듯이 Scouter는 UDP와 TCP를 모두 사용합니다.
>
> * 성능정보는 UDP를 통해 Collector에 전송되며, TCP 연결은 Client 프로그램을 통해 사용자가 Agent에 특정 요청을 보낼때 사용됩니다.
> * 연결은 UDP, TCP모두 Agent가 수행하며, 따라서 Agent와 Collector 사이에 방화벽등이나 보안 정책이 적용되어 있다면 TCP및 UDP 6100 포트가 허용되어 있어야 합니다.



5. Java Agent 실행

Java Agent는 단독으로 실행되는 것이 아니라, 모니터링할 Java Program이 실행될 때 attach되어 모니터링을 수행합니다. 따라서 Java Program에서 scouter agent를 인식할 수 있도록 명령행 옵션을 설정해야 합니다.

* -javaagent:/[somewhere]/scouter.agent.jar

그리고 하나의 VM에 여러개의 java프로그램을 실행한다면 보통 scouter agent의 환경설정 파일경로와 이름을 지정해주게 됩니다.

* 설정 파일 경로 지정
  * -Dscouter.config=/[somewhere]/mySampleConfig.conf
  * 설정 파일 경로를 지정하지 않은 경우 기본값
    * [directory of scouter.agent.jar]/conf/scouter.conf
* 모니터링 대상(Object) 이름 지정
  * -Dobj_name = scouterWebDemo1
  * 기본값 : java1 또는 tomcat1

설정파일의 위치는 `${SCOUTER_HOME}\scouter\agent.java\conf`에 원하는 이름으로 만들어 놓으면 됩니다. 저는 `jboss_7_3_standalone.conf `로 만들었습니다. 내용은

```conf
#Scouter Server IP Address(Default: 127.0.0.1)
net_collector_ip = 127.0.0.1

#Scouter Server Port(Default: 6100)
net_collector_udp_port=6100
net_collector_tcp_port=6100

#Scouter Name(Default : tomcat1)
obj_name = jboss_7_3_standalone

trace_interservice_enabled=true
hook_method_patterns=org.mybatis.jpetstore.*.*
```

로 만들어 놓습니다.



위 설정들을 우리가 실행할 WAS의 실행파일 옵션에 적어놔야 합니다.

```
set "JAVA_OPTS=
-javaagent:D:\scouter-all-2.12.0.1.SNAPSHOT\scouter\agent.java\scouter.agent.jar 
-DScouter.config=D:\scouter-all-2.12.0.1.SNAPSHOT\scouter\agent.java\conf\jboss_7_3_standalone.conf  
-Dobj_name=jboss_7_3_standalone 
-Djboss.modules.system.pkgs=org.jboss.byteman,scouter"
```

[scounter_java_agent 설정시 주의사항](https://atl.kr/dokuwiki/doku.php/scouter_java_agent_%EC%84%A4%EC%A0%95%EC%8B%9C_%EC%A3%BC%EC%9D%98%ED%95%A0%EC%A0%90)

위의 설정대로 적어놓고 scouter server, agent host를 실행한 후, WAS를 실행 합니다. 

![scouter5](https://user-images.githubusercontent.com/54675591/120755419-eaa60c80-c548-11eb-9438-28c2c2c1ac11.PNG)

Scouter 로그가 뜨면 제대로 실행이 된것 입니다.

그 후 Scouter Client에 접속하면 

![scouter6](https://user-images.githubusercontent.com/54675591/120761367-14166680-c550-11eb-8e66-8fdec405b1c3.PNG)

 `jboss_7_3_standalone.conf `에 설정한 이름대로 Object가 추가된 것을 볼 수 있습니다.

