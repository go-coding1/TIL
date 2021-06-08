# Scouter APM - 기본 항목 모니터링

### 1. "Performance Counter"와 "Object Request"

Scouter에는 모니터링 항목을 크게 두가지로 구분합니다. "Performance Counter", 그리고 "Object Request"입니다. "Performance Counter"는 시간에 따라 변하는 값을 실시간 차트 형태로 보여주며, "Object Request"는 사용자가 특정 성능 정보를 요청하여 조회하는 기능입니다.

그리고 보통 "Performance Counter"에 포함하기도 하지만, 그 성격이 전혀 다른 특수한 몇가지 기능이 있습니다. (XLog, Active Service EQ 등)

이러한 기능은 조금 복잡한 부분이 있으므로 다른 포스트에서 다루도록 하겠으며 여기서는 기본적인 모니터링 항목에 대해서 설명하도록 하겠습니다.

이러한 기능들은 상단메뉴에서도 접근이 가능하지만 "Object View"에서 콘텍스트 메뉴에서 접근하는 것이 더 직관적입니다.

**1.1 Performance Counter**

Performance Counter는 시계열 성능 메트릭을 의미합니다.

* Counter View를 Collector의 콘텍스트 메뉴에서 여는 경우, 해당 Object Type의 인스턴스 전체에 대해 하나의 차트로 보여줍니다. (예를 들어 TPS 차트를 선택한 경우 동일한 Object Type인 tomcat1, tomcat2,... tomcatX에 대한 TPS가 하나의 차트로 보여집니다.)
* 각 Object의 콘텍스트 메뉴에서 여는 경우 해당 Object에 대한 Counter View만 보여줍니다.
* 어떠한 View는 Collector메뉴에서만 열 수 있거나 혹은 Object 메뉴에서만 열 수 있습니다.
  * XLog 및 Active Service EQ등은 Collector메뉴에서만 열 수 있습니다.
  * Heap Total Usage, Sys/User CPU등은 Object 메뉴에서만 열 수 있습니다.

![scouter7](https://user-images.githubusercontent.com/54675591/120766765-82115c80-c555-11eb-9313-18bf9b2e6e9a.png)

<p align="center">< 그림. Collector의 콘텍스트 메뉴에서 Performance Counter 열기></p>

![scouter8](https://user-images.githubusercontent.com/54675591/120767047-c866bb80-c555-11eb-8d96-cee356531f7f.png)

<p align="center">< 그림. 개별 Object의 콘텍스트 메뉴에서 Counter 열기></p>

>* Scouter에서 중요한 개념중의 하나가 Object Family와 Object Type(monitoring group type)입니다.
>
>* Object Family는 모니터링 항목 특성에 따른 모니터링 대상의 종류를 나타냅니다. 예를 들면 Host와 Java를 Object Family로 볼 수 있습니다.
>
>* Object Type(monitoring group type)은 Family의 하위 개념으로써, 예를 들어 Object Familiy가 Host라면 이 하위의 Object Type은 Linux, Windows등이 될 수 있고, Java 라면 tomcat, jetty등이 될 수 있습니다.
>
>* 또한 Object Type은 사용자 지정이 가능합니다.
>
>  이를 이용해 한번에 모니터링 할 대상을 동일한 Object Type으로 묶기도 합니다.
>
>* 위 두가지 개념은 Scouter에서 중요하게 다루어지므로 기억하고 있는게 좋습니다. **주로 Family는 모니터링 항목에 과련되어 있으며, Type은 어떠한 대상들을 하나의 차트에서 모니터링 할 것인가 **와 관련이 있습니다

> Scouter 1.7 이상에서는 Object Type이라는 용어 대신 Monitoring Group Type 이라는 용어를 사용하기도 합니다.

**1.2 Object Request**

Object Request는 원하는 시점에 사용자가 각 Object들에 특정 정보를 요청하는 기능입니다. 요청할 수 있는 정보는 당연히 Object Family에 따라 달라집니다.

Object Request는 각 모니터링 대상(Object)의 콘텍스트 메뉴에서 사용이 가능합니다.

![scouter9](https://user-images.githubusercontent.com/54675591/120772297-1df19700-c55b-11eb-97a0-8c89ebfcd3ff.png)

### 2. Host 모니터링

**2.1 Host의 성능 Counter**

* Counter의 항목명으로 알 수 있으므로 따로 설명하지 않습니다.

  ![scouter10](https://user-images.githubusercontent.com/54675591/120772568-6741e680-c55b-11eb-9d08-61a3c1be5313.png)

**2.2 Host의 Object Request에서 제공하는 기능**

![scouter11](https://user-images.githubusercontent.com/54675591/120772824-acfeaf00-c55b-11eb-8f8d-7b6521ac0af9.png)

* ENV : OS의 환경 변수를 조회합니다.
* Disk Usage : Disk 사용량을 조회합니다.
* Top : 요청 시점의 Process를 조회합니다.

![scouter12](https://user-images.githubusercontent.com/54675591/120773028-dfa8a780-c55b-11eb-8fbe-e015cb8e8ba5.png)

<p align="center">< 그림. Top Process 조회></p>

### 3. Java 모니터링

**3.1 Java 모니터링에서 제공하는 성능 Counter#1**

Counter의 이름으로 대부분이 파악이 가능하므로 부가적인 설명이 필요한 것들에 대해서만 언급하도록 하겠습니다.

![scouter13](https://user-images.githubusercontent.com/54675591/120773481-5776d200-c55c-11eb-8eb2-2baae46e7d18.png)

<p align="center"><그림. Java 성능 Counter #1></p>

*  Active Service : 측정 순간에 서버에서 동시에 실행중인 서비스의 개수를 의미합니다.

* Elapsed90% : 90%로 느린 응답시간을 나타냅니다. (예를 들어 100개의 요청이 들어왔다면 이중 90번째로 느린 요청의 응답시간입니다.)

* Process CPU : 해당 Java Process가 사용하는 CPU%입니다.

* Queuing Time

  * Java 프로그램(주로 Servlet container인 경우)의 앞단의 reverse proxy나 gateway서버로부터 java프로그램으로 요청이 들어오기 까지의 시간을 의미합니다. (reverse proxy나 gateway 서버에 특별한 설정이 필요합니다.)

* Recent User

  * 일반적으로 말하는 concurrent user와 유사한 개념입니다.

    HTTP로 서비스되는 환경에서는 concurrent user라는 개념이 모호하므로, Scouter에서는 최근 5분간 1번이라도 요청을 보낸 사용자수를 Recent User라는 개념으로 제공합니다.

* Today Visitor

  * 특정 날짜의 유니크 방문자 수를 보여줍니다.

**3.2 Java 모니터링에서 제공하는 성능 Cotainer#2**

Java 모니터링에서는 기본적인 시계열 차트외에 애플리케이션에 대해 보다 상세한 정보를 제공하는 몇가지 차트가 존재합니다.

![scouter14](https://user-images.githubusercontent.com/54675591/120980379-8639b600-c7b1-11eb-9e3c-ad1295c46004.png)

* Active Service EQ

  * 현재 시점에 얼마나 많은 서비스가 동시에 수행되는지를 한 눈에 파악하기 위한 이퀄라이저 형태의 차트입니다. 이 차트는 더블클릭하게되면 현재 수행되는 서비스를 **"Active Service View"**에서 목록 형태로 보여주게 됩니다.

    ![scouter15](https://user-images.githubusercontent.com/54675591/120980988-25f74400-c7b2-11eb-9499-8b1df62659a5.png)

    <p align="center"><그림. Active Service EQ></p>

* Active Service View

  * 현재 시점에 서버에서 수행중인 서비스 목록 및 이에 대한 상세 정보입니다/

    서비스명 및 수행중인 SQL이나 호출하고 대기중인 원격 Service URL이 보여지게 되며, 코드의 어떤 부분이 수행중인지 알 수 있도록 StackTrace 및 Thread의 Lock 정보등이 제공됩니다.

    ![scouter16](https://user-images.githubusercontent.com/54675591/120981457-a61da980-c7b2-11eb-8521-ea31ff2d1532.png)

    <p align="center"><그림.Active Service Detail></p>

* XLog

  * 모든 요청에 대해 scatter 차트형태로 보여주며 이를 통해 어플리케이션의 상태를 직관적으로 파악할 수 있습니다. XLog 차트를 통해 애플리케이션 상태를 파악하고, 상세한 정보는 개별 요청의 Profile View에서 확인하게 됩니다. Scouter의 제품 목표 중 하나가 XLog 차트를 이용하여 애플리케이션에서 발생하는 문제를 빠르게 확인하고 원인을 찾아내는 것이기 때문에 여기서 제공하는 기능이 상당히 많습니다.

![scouter17](https://user-images.githubusercontent.com/54675591/120982738-f9442c00-c7b3-11eb-879a-b629ae9d9f3b.png)

<p align="center"><그림.XLog View></p>

![scouter18](https://user-images.githubusercontent.com/54675591/120982840-18db5480-c7b4-11eb-85fb-4103ee4502c8.png)

<p align="center"><그림.XLog List View></p>

<img src="https://user-images.githubusercontent.com/54675591/120983002-3f998b00-c7b4-11eb-9e54-735d90db556a.png" alt="scouter19" style="zoom:100%;" />

<p align="center"><그림. Profile View></p>

**3.3 Java모니터링에서 제공하는 Object Request**

![scouter20](https://user-images.githubusercontent.com/54675591/120983292-85eeea00-c7b4-11eb-8653-012b632f48fd.png)

* Thread List

  * 해당 Java Process의 Thread 목록을 테이블 형태로 보여줍니다.

* Active Service List

  * 요청 시점에 실행중인 Service 목록을 보여줍니다. Active Service EQ를 더블클릭해서도 접근할 수 있습니다.

* Loaded Class List

  * 로드한 Class의 목록을 보여줍니다.
  * 변경한 Hooking 옵션을 Runtime에 적용하기 위해 Class를 Redefine하는 기능을 제공합니다.

* Heap Histogram

  * Heap Histogram을 보여줍니다.

* Env

  * JVM의 환경변수를 조회합니다.

* Socket

  * 해당 Process에서 연결한 Socket의 정보를 보여줍니다.
  * Socket#connect의 stacktrace를 생성하고 조회할 수 있습니다.

* System.GC

  * System.GC()를 호출합니다. 

    >System.gc()를 호출하면 명시적으로 가비지 컬렉션이 일어나도록 코드를 삽입할 수 있지만, 모든 스레드가 중단되기 때문에 코드단에서 호출하는 짓을 하면 안된다.

* Heap Dump

  * Heap Dump를 생성합니다.

* File Dump

  * 위에서 언급한 기능들을 수행하여 파일로 저장합니다.

---

### 1.차트의 종류

Scouter의 performance counter는 8가지 종류의 차트를 제공하며, 처음에 열리는 차트는 실시간 성능 정보를 제공해주는 **"Realtime-Current"**차트 입니다.

* Real Time 차트 : 현재 시점까지의 데이터를 보여주며 실시간 갱신되는 차트
  * Current : 최근 5분간의 데이터를 2초 간격으로 갱신(Default)
    * All : 각 인스턴스별 값을 보여줍니다.
    * Total : 모든 인스턴스 값의 합계를 보여줍니다.
  * Today : 오늘의 0시 부터 현재까지의 데이터를 보여줍니다.
    * All : 각 인스턴스별 값을 보여줍니다.
    * Total: 모든 인스턴스 값의 합계를 보여줍니다.
* History 차트 : 과거 특정 시점의 데이터를 조회합니다.
  * Daily : 과거 특정 시점의 1일~수개월 데이터를 5분 통계로 보여줍니다.
    * All : 각 인스턴스별 값을 보여줍니다.
    * Total : 모든 인스턴스 값의 합계를 보여줍니다.
  * Past : 과거 특정 시점의 5분~4시간의 데이터를 2초 정밀도로 보여줍니다
    * All : 각 인스턴스별 값을 보여줍니다.
    * Total : 모든 인스턴스 값의 합계를 보여줍니다.

![scouter21](https://user-images.githubusercontent.com/54675591/121103913-e37a4980-c83b-11eb-9e09-042092aa82c7.gif)

<p align="center"><그림.Real Time Current></p>

![scouter22](https://user-images.githubusercontent.com/54675591/121103993-0a388000-c83c-11eb-9c7d-89691ed7ef64.gif)

<p align="center"><그림. Real Time Total></p>

![scouter23](https://user-images.githubusercontent.com/54675591/121104072-3522d400-c83c-11eb-900a-94a2e48fa369.png)

<p align="center"><그림. Today Total></p>

<p align="center">"Today Total" 차트에서 어제 데이터는 회색으로 표시됩니다.</p>

![scouter24](https://user-images.githubusercontent.com/54675591/121104237-90ed5d00-c83c-11eb-8290-ebd920a2f21b.png)

<p align="center"><그림. Past All></p>

![scouter25](https://user-images.githubusercontent.com/54675591/121104300-b24e4900-c83c-11eb-851d-41ba9368404a.png)

<p align="center"><그림. Daily All></p>

### 2. 차트 다루기

**2.1 Performance Counter 차트 다루기**

* 하나의 차트에서 콘텍스트 메뉴를 통해 다른 유형의 차트를 열 수 있습니다.

  ![img](https://4.bp.blogspot.com/-fz1AdOx7YVo/WXl_tYJPi4I/AAAAAAAAc4k/oRiuHDZu4-8ZLgWetWxWFVsRgNkF15JbQCLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2017-07-27%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B2.52.28.png)

* 차트의 라인을 클릭하면 상세 정보를 볼 수 있습니다.

* 어떤 차트는 차트의 Y축을 더블클릭하면 Y축의 높이를 조정할 수 있습니다.

  ![img](https://4.bp.blogspot.com/-jTPnFMAXmf8/WXmAraBrBKI/AAAAAAAAc4s/JcBExwpSvFIHlBf8GSpLhwazuMUtMiwHgCLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2017-07-27%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B2.55.27.png)

**2.2 차트의 재배열 및 Perspective 저장**

파트를 재배열하여 대시보드를 구성할 수 있습니다.

* 대시보드의 각 차트를 드래그 & 드롭 하여 대시보드를 손쉽게 재구성 할 수 있습니다.
* 재구성한 대시보드는 "Perspective"로 저장하여 재사용 할 수 있습니다.
  * perspective 이름에서 오른쪽 마우스 버튼 >Save as
* "Perspective"는 내보내기(Export) 및 가져오기(Import)를 할 수 있어, 팀원과 공유하거나 백업해 놓을 수 있습니다.

> * Scouter Client는 여러 디렉토리로 복사하거나 이름을 변경하여 여러개를 실행할 수도 있습니다.
> * OSX버전의 경우 모든 클라이언트가 (perspective 설정이 포함되어있는) workspace를 공유합니다. 만약 각기 다른 설정을 사용하고 싶다면 패키지 파일내의 Contents/Eclipse/scouter.ini 파일에서 "-data@user.home/.scouter" 부분을 삭제하면 됩니다.
> * 반대로 Windows 버전의 경우 Client 프로그램을 재설치하거나 업그레이드 할때 저장해놓은 perspective 설정을 매번 Import 하기 귀찮다면 scouter.ini를 수정하여 workspace 경로를 공통적이 경로로 바라보게 합니다.

