# Active Service와 XLog

APM이 다른 모니터링 도구와의 차이점 중 하나는 발생한 문제의 결과가 아닌 원인에 접근할 수 있다는 것입니다.

이름 위해 Scouter에서는 어플리케이션이 지금 어떤 코드를 실행하고 어디서 지연되고 있는지를 파악할 수 있는 "Active Server 모니터링"과 이미 응답한 요청에 대해 상세 분석할 수 있는 Scatter 형식의 차트인 "XLog"를 제공합니다

### 1. Active Service 모니터링

"Active Service"란 현재 시점에 애플리케이션에서 수행되고 있는 요청을 나타냅니다.

이에 대한 모니터링은 주로 "Active Service EQ" 차트로 하게되는데, 서비스 지연으로 인한 장애를 가장 먼저 발견할 수 있는 차트이기도 합니다.

![Scouter Active Service EQ.](https://4.bp.blogspot.com/-hHfes-JWCwc/WXqJgUqiwtI/AAAAAAAAc6A/mgnrUViOwiAsVjnbLEmay_s3udZPBhb4wCLcBGAs/s320/2017-07-28%2B09_46_34.gif)

<p align="center"><그림. Active Service EQ></p>

위 그림은 다섯개의 인스턴스를 모니터링하고 잇음, 숫자는 현재 동시에 실행중인 서비스의 개수를 의미합니다. 지연되는 서비스는 3초 이후에는 노란색, 7초 이후에는 빨간색으로 표시가 됩니다.

여기서 실행중인 서비스의 정보를 알고 싶다면 상세히 보고싶은 인스턴스를 더블클릭하면 "Active Service List" 화면이 열리게됩니다.

![Scouter's Active Service List](https://3.bp.blogspot.com/-NrD03ZnTwBo/WXqL-liD1bI/AAAAAAAAc6M/bXpgVjRG36g2iiQMxKgquJ3y8azQwptcwCLcBGAs/s640/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2017-07-28%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB%2B9.53.53.png)

<p align="center"><그림.Active Service List></p>

여기서는 호출된 서비스명과 현재까지 수행중인 시간(ms), 요청자 IP, 쿼리나 다른 서비스를 호출하고 대기중이라면 해당 쿼리나 서비스의 이름, Thread의 상태와 이름등이 보여집니다.

* Service : 요청한 서비스의 이름
* Elapsed : 현재 시점까지 수행중인 시간
* Note : 현재 수행중인 쿼리 혹은 원격 호출하고 대기중인 다른 서비스
* CPU : Thread가 생성된 후 현재까지 사용한 CPU
* IP : 요청자 IP
* State/Name : Thread의 상태와 이름

여기서 상세하게 보고 싶은 서비스가 있다면 해당 서비스를 더블클릭하면 아래와 같은 상세화면이 열립니다.

![img](https://3.bp.blogspot.com/-PuqK2brVc2M/WXqOrciNeHI/AAAAAAAAc6U/xgkqXR_VhR4Zdy5fhzcMAZVM5SSKPh8gACLcBGAs/s400/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2017-07-28%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB%2B10.07.41.png)

위 예에서는 HttpClient가 서비스 요청하였고 응답을 대기하고 있다는 것을 StackTrace를 통해 알 수 있습니다.

이와 같이 "Activer Service" 모니터링을 통해서는 현재 시스템이 응답이 지연되고 있는지를 바로 파악할 수 있으며 상세 정보를 통해 어디서 지연되는지를 코드 레벨에서 정확히 파악할 수 있습니다.

모니터링 시점에 지연되는 부분이 잇다면 여기서 원인을 파악하는 것이 가장 좋은 방법입니다. 하지만 많은 경우 지나간 이슈에 대해 원인을 파악해야 할 필요가 있으며 이런 경우에 활용하는 것이 XLog 차트입니다.

### 2. XLog 차트 모니터링

XLog 차트는 모든 요청에 대해 하나의 점으로 표현하는 일종의 scatter차트로서 x축에는 요청이 종료된 시간을, y축에는 주로 해당 요청의 응답시간을 표현합니다.

각 점의 색은 object마다 다르며 빨간색은 에러를, 회색은 비동기 thread를 나타냅니다.(scouter는 비동기 thead에 대한 연결 추적을 지원합니다.)

![img](https://github.com/scouter-project/scouter/raw/master/scouter.document/img/quickstart/xlog1.png)

위 그림에서 보이듯이 XLog의 각 점들을 마우스로 드래그하면 선택한 점들의 목록을 확인 할 수 있으며 각 XLog들은 하나의 서비스요청에 대한 메타 정보를 가지고 있습니다.

이 메타 정보를 통해 선택한 점들이 어떤 서비스인지 응답시간은 얼마나 걸렸는지, SQL문이나 API호출시의 응답시간은 얼마였는지 등의 1차적인 정보를 확인할 수 있습니다.

* XLog의 주요 메타 정보
  * Service : service명
  * elapsed : 응답시간
  * endtime : 요청의 종료시간
  * ipaddr : 요청자의 ip
  * userAgent
  * sqlCallCount,sqlCallTime : 실행한 SQL 개수와 수행시간
  * cpu : 해당 요청을 처리하는 동안 사용한 cpu time
  * kbytes : 해당 요청을 처리하는 동안 사용한 heap memory
  * thread : 처리한 thread명
  * country, city : geoip정보(서버에 geoip_data_city_file 옵션 설정시)
  * queuing time : proxy 서버로 부터 인입까지 걸린 시간(설정시)
  * login, desc, text1~5 : agent plugin을 통해 사용자가 설정한 정보

XLog의 목록을 통해 기본적인 정보를 파악하였다면 상세 프로파일 정보를 통해 보다 더 정확한 정보를 확인할 수 있습니다. 상세프로파일은 기본적으로 SQL 및 Socket 연결, http call 및 redis call, aync thread call 등과 같은 연계에 대한 정보를 보여주며, 추가적인 설정을 통해 특정한 패턴의 메소드를 프로파일하게 할 수도 있습니다.

![img](https://github.com/scouter-project/scouter/raw/master/scouter.document/img/quickstart/profile1.png)

<p align="center"><그림. 프로파일에서 보여지는 메소드 및 SQL></p>

![img](https://github.com/scouter-project/scouter/raw/master/scouter.document/img/quickstart/interservice1.png)

<p align="center"><그림. 프로파일에서 보여지는 외부 API호출 정보 및 Socket 연결 정보></p>

만약 호출되는 API의 서버에도 Scouter가 설치되어 있다면, Scouter는 spanid를 발행하여 각 서비스들(혹은 분기된 비동시 thread들)을 연결하여 추적할 수 있게 합니다.

위 그림에서 call 링크로 표현된 부분을 클릭하게되면 호출한 서비스의 profile을 보여주게 됩니다.

또한 위 그림에서 gxid 링크를 클릭하게 되면 서비스의 시작점부터 호출된 서비스 및 각 서비스가 사용한 테이블의 CRUD 현황을 하나의 다이어그램으로 보여주게 됩니다.

![img](https://github.com/scouter-project/scouter/raw/master/scouter.document/img/quickstart/interservice2.png)

<p align="center"><그림. XLog Service flow View></p>

> * Scouter의 프로파일에선 비동기 thread 호출에 대해 외부 API 호출과 동일한 방식인 외부 링크형식으로 보여줍니다. 이를 하나의 화면에 tree 형식으로 보여주지 않는 이유는 (API 호출도 마찬가지이지만) thread 호출이 완료되기를 blocking 하여 기다리지 않는 경우나 여러개의 thread 호출이 동식에 발생하고 이중 일부 호출에 대해서는 bloking 한 후 또 다른 thread 호출이 발생하는 등 선후 관계가 서로 뒤바뀌거나 시간 순서대로 표현하기가 모호한 부분등을 나타내는 데에는 링크 방식이 더 이해하기 편하다고 판단하였기 때문입니다.



### XLog 활용

Scouter 개발시 가장 중점에 두었던 것 중 하나가 "XLog 차트 안에서 모든 문제를 다 해결할 수 있도록 하자" 였습니다. 그래서 XLog 파트는 상당히 많은 기능을 가지고 있으며 그중 중요한 기능들에 대해 설명하도록 하겠습니다.

**1. XLog의 조작**

1. 키보드를 통한 간편 이동

* 실시간 XLog 차트에서 키보드를 사용하여 XLog 차트의 시간을 이동시킬 수 있습니다. 이를 통해 가까운 과거 시점으로 빠르게 이동이 가능합니다. (큰 시간을 이동하여야 하면 Load History 메뉴를 사용하여야 합니다.)
  * 좌우 화살표 : 한번 누르는 경우 10초를 이동합니다.
  * 상하 화살표 : 한번 누르는 경우 일정한 비율로 Y축의 스케일을 조절합니다.

2. Y축 항목 변경

![img](https://3.bp.blogspot.com/-Z-n4-RaGGq4/Wv_bhMx-K2I/AAAAAAAAd0s/MZRXKmWLIOoKiEFp53wv246MMUqGdymbwCLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-19%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B5.08.17.png)

<p align="center"><그림. XLog Y축 항목 변경></p>

* Y축 항목을 응답시간(Elapsed Time)이 아닌 다른 값으로 변경할 수 있습니다.
  * 현재 서비스가 CPU bound 인지 혹은 SQL이나 Api call bound인지를 한눈에 파악할때 주로 사용하게 됩니다.
  * 혹은 SQL호출 회수가 많은 서비스를 골라내거나, 메모리 사용량이 많은 서비스를 골라낼때 사용하기도 합니다.

3. Load History

* 과거 특정 시점의 XLog 차트를 로드하기 위해 사용합니다.

4. Summary

![img](https://2.bp.blogspot.com/-t3z3QGYIFTw/Wv_dBuxUZCI/AAAAAAAAd08/AiKMNKrc8jw5yBkbVSlHOgWnyoACdu4SQCLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-19%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B5.14.46.png)

<p align="center"><그림. XLog 통계></p>

* 화면에 표시된 XLog 점들에 대한 통계를 바로 확인할 수 있는 기능입니다.
  * 예를 들면 각 서비스에 대한 총 호출 건수 및 총 응답시간, 평균 응답시간, SQL 및 API 호출에 대한 평균 응답시간 등을 확인할 수 있습니다.

5. Filter

XLog를 통한 분석시 가장 많이 사용되는 기능중의 하나입니다.

원하는 조건의 XLog 점들만 보여지도록 하는 기능으로 필터를 적용하면 XLog 차트의 배경색이 메메랄드 색으로 변경됩니다.

![img](https://3.bp.blogspot.com/-CSJS3qUaNsU/Wv_ffBbGsSI/AAAAAAAAd1I/_k0Pm4H7auQ9oR1a6vmTcuMFwTgH0n-PQCLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-19%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B5.23.47.png)

<p align="center"><그림. XLog Filter></p>

* Service : Service명으로 Filter 적용

* IP : IP로 Filter 적용
* StartHMS : 요청 시작 시간으로 Filter 적용
* User-Agent : User agent 값으로 Filter 적용
* Login, desc, text1~5 : agent plugin으로 적용한 사용자 정의 값에 대한 filter 적용
* 빠른 필터: SQL이 포함된 요청, API 호출이 포함된 요청

> * 각 값은 특정한 와일드 카드 형식으로 표현이 가능합니다.(당연히 정확한 값의 경우가 검색이 빠릅니다.)
>   * 단어의 가장 앞, 가장 끝, 그리고 중간에 하나씩 최대 3개의  * 을 사용할 수 있습니다.
>   * 예를 들어 /user/100<GET>라는 효청은/user\*,\*user\*,또는 \*us\*r\*등으로 검색이 가능합니다.

6. Search

과거 시점에 XLog 목록을 특정 조건으로 조회하는 기능입니다. (반면에 Load History는 과거 시점의 XLog 차트를 열어주는 기능임)

![img](https://4.bp.blogspot.com/-w97Ecr5aR_c/Wv_lJ2mQlLI/AAAAAAAAd1U/NaLeTIyXlFwIEsMQUu-5x6fmTNoaijG0gCLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-19%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B5.49.19.png)

<p align="center"><그림.XLog Search></p>

---

**2. XLog 관련 주요 설정**

XLog의 목적은 시스템에 발생하는 문제를 빠르게 인지하고 원인을 파악하여 해결하기 위함입니다. 이러한 목적을 잘 달성하기 위해서는 모니터링 대상 시스템을 잘 추적할 수 있도록 설정하는 것이 도움이 됩니다. 단 추가된 설정만큼 저장되는 데이터의 양이 많아지고 과도한 정보를 profiling하는 것은 오히려 분석에 방해가 되는 경우가 있으므로 시스템 상황에 맞게 적절히 조절할 것을 권장합니다.

> **Profiling(프로파일링)이란?**
>
> 프로파일링은 동적 코드 분석의 한 형태입니다. 프로파일링은 모든 개발 또는 배포 시나리오에서 성능 데이터를 수집하는 방법입니다. 

1. method profiling

Scouter의 profiling은 주로 외부 연동 정보를 위주로 profiling을 하게 되므로 보다 상세한 profiling이 필요한 경우 보통 가장 먼저 설정하는 부분입니다.

모니터링 대상 코드를 분석 후 아래 옵션을 통해 추가적으로 profiling할 method를 지정해 줍니다.

아래 옵션을 지정하면 profile에 지정한 method가 추가됩니다.

 `${SCOUTER_HOME}\scouter\agent.host\conf` 위치에 있는 .conf 파일에 적용시킬 수 있다.

`hook_method_patterns=comp.order.*Controller.*, comp.order.*Service.*, comp.order.*Repository.*`

> **Patterns 설정 방법**
> Scouter 설정 중 이름이 patterns로 끝나는 경우는 대부분 아래와 같은 형식으로 사용할 수 있습니다.
>
> - 예시
>
> - - ***com.sc.\*.\**** : com.sc. 으로 시작하는 패키지내의 모든 class의 모든 method
>   - ***com.sc.order.Order.\**** : com.sc.order.Order class의 모든 method
>   - ***com.sc.order.\*Service.\**** : com.sc.order. 패키지의 class중 Service로 끝나는 class의 모든 method
>   - ***com.sc.order.Order.findOrder\*** : com.sc.order.Order class의 method중 findOrder method
>   - ***com.sc.order.Order.find\**** : 인식되지 않음, method명에는 *를 조합하여 사용할 수 없음.

method profiling은 public method에만 작동합니다. 다른 접근 제한데에 대한 profiling을 허용하려면 다음 옵션을 사용합니다.

```conf
hook_method_access_public_enabled=true
hook_method_access_private_enabled=false
hook_method_access_protected_enabled=false
#Activating default Method hooking
hook_method_access_none_enabled=false
```

> Scouter 옵션 중 "hook_"로 시작하는 옵션은 모두 모니터링 대상 object(주로 tomcat등의 java application)를 재시작해야 적용이 됩니다.

또한 보다 상세한 제어가 필요한 경우 아래 옵션들을 복합적으로 사용하게 됩니다.

```conf
hook_method_exclude_patterns=
hook_method_ignore_classes=
hook_method_ignore_prefixes=get,set #기본값
```

---

2. http header와 form parameter profiling

필요한 경우 http header 및 form parameter 정보를 profiling 할 수 있습니다.

```conf
#http header 정보를 profile합니다. profile_http_header_key가 정의되지 않았으면 전체 hearder를 profile 합니다.
profile_http_header_enabled=false
#profile할 http header의 이름들을 comma로 구분하여 기입합니다.
profile_http_header_keys=
```

Form parameter 또는 query string을 profiling 하기 위해서는 아래 옵션을 사용합니다.

```conf
profile_http_parameter_enabled=false
profile_http_querystring_enabled=fasle
```

추가로 Spring controller method의 parameter를 profiling하기 위해서는 아래 옵션을 사용합니다.

```conf
#Spring의 request mapping method의 parameter를 profiling합니다.
#profiling되는 값은 각 파라미터 오브젝트의 toString() method 값입니다.
profile_spring_controller_method_parameter_enabled=false
```

그리고 XLog 정보에는 ip 정보가 포함되는데 proxy등을 경유하는 경우 사용자 IP를 (x-forwarded-for 같은) http header에서 구해오도록 설정 할 수 있습니다.

```conf
trace_http_client_ip_header_key=
```

3. XLog를 error로 마킹하기

XLog는 아래의 상황에서만 error로 마킹됩니다.

* Servlet 및 Filter에서 까지 exception handling이 되지 않은 경우
* 외부로 요청한 통신에서 에러가 발생한 경우
* SQL Exception이 발생한 경우
* SQL 수행 시간이 매우 느린 경우(30초)
* SQL의 fetch 건수가 많은 경우(10000건)

추가적인 경우에 error 마킹을 하려면 아래 옵션들을 사용합니다.

```conf
#error로 마킹할 Exception class pattern을 지정합니다.
#상속받은 Exception class의 이름까지 검색합니다.
hook_exception_class_patterns = my.app.SereiousException,my.app.svc.*Exception
#위 패턴에서 제외할 패턴을 지정합니다.(예를 들어 MyPjtException을 지정하였으나, 이를 상속받은 MyPjtBizException은 제외하려면 여기에 설정합니다.)
hoo_exception_exclude_class_patterns =
#Exception handler method의 pattern을 지정합니다. 이 메소드로 전달되는 Exception class의 정보로 error 정보를 profiling 합니다.
hook_exception_handler_method_patterns=my.app.myHandler.handleException
#위 메소드에 전달되는 Exception 중 error 마킹에서 제외할 class 정보를 입력합니다.
hook_exception_handler_exclude_class_patterns=
```

또한 error발생시 profile에 full stack trace를 남기거나 기본 제공되는 error옵션을 변경하려면 아래 설정들을 사용하면 됩니다.

```conf
profile_fullstack_hooked_exception_enabled=false
xlog_error_on_sqlexception_enabled=tue
xlog_error_on_apicall_exception_enabled=true
xlog_error_on_redis_exception_enabled=true
xlog_error_sql_time_max_ms=30000
xlog_error_jdbc_fetch_max=10000
```

---

4. XLog샘플링하기(일부의 XLog만 저장하기)

샘플링을 해야하는 경우 아래 옵션들을 적절히 조합하면 필요한 정보들을 최대한 유지하면서 휴율적으로 샘플링을 할 수 있습니다.

```conf
##### [XLog Sampling] #####
xlog_sampling_enabled=false
#xlog는 유지하고 profile 정보만 샘플링한다.
xlog_patterned_sampling_only_profile=false
xlog_sampling_step1_ms=100
xlog_sampling_step1_rate_pct=3
xlog_sampling_step2_ms=200
xlog_sampling_step2_rate_pct=10
xlog_sampling_step3_ms=500
xlog_sampling_step3_rate_pct=30
xlog_sampling_over_rate_pct=100

##### [XLog Patterned Sampling] #####
xlog_patterned_sampling_enabled=false
xlog_patterned_sampling_service_patterns=
#xlog는 유지하고 profile 정보만 샘플링한다.
xlog_patterned_sampling_only_profile=false
xlog_patterned_sampling_step1_ms=100
xlog_patterned_sampling_step1_rate_pct=3
xlog_patterned_sampling_step2_ms=1000
xlog_patterned_sampling_step2_rate_pct=10
xlog_patterned_sampling_step3_ms=3000
xlog_patterned_sampling_step3_rate_pct=30
xlog_patterned_sampling_over_rate_pct=100
```

* 전체 XLog를 대상으로 샘플링
  * xlog_sampling으로 시작하는 옵션을 사용합니다.
  * 응답시간에 따라 4단계로 샘플링하는 비율을 지정할 수 있습니다.
* 특정 패턴의 url에 샘플링
  * 전체 샘플링보다 우선하여 적용됩니다.
  * xlog_patterned_sampling으로 시작하는 옵션을 사용합니다.

> XLog 샘플링 옵션을 적용하여도 TPS나 서비스 호출건수등의 카운터 정보에는 영향을 주지 않습니다.

그리고 xlog discard 옵션을 사용하면 TPS등에 반영되지도 않습니다. 보통 health check 요청등을 무시하기 위해 사용됩니다.

```conf
xlog_discard_service_patterns=
#error 발생시에는 xlog에 기록한다.
xlog_discard_service_show_error=true
```

---

**3. Scouter 서버의 데이터 저장공간 설정**

xlog와 profile은 서버 저장 공간 중 가장 많은 부분을 차지하는 데이터입니다.

디스크 용량이 한도를 넘어서게 되면 무조건 과거 데이터부터 삭제하게 되므로 한도 용량을 넘어서지 않도록 적절히 조절하여 설정하는 것이 중요합니다.

일반적으로 profile의 저장 기간을 가장 짧게 설정하고 성능 카운터의 정보를 가장 길게 유지합니다.

아래 설정들은 java agent 설정이 아닌 collector server의 설정입니다.

```conf
#데이터 저장소의 위치는 scouter server가 설치된 경로 하위의 database 디렉토리입니다.
db_dir=./database
#디스크의 특정 용량을 초과하면 과거 데이터부터 삭제합니다.
mgr_purge_disk_usage_pct=90
#프로파일 데이터를 유지하는 기간(일반적으로 가장 큰 사이즈의 데이터입니다.)
mgr_purge_profile_keep_days=10
#XLog를 유지하는 기간
mgr_purge_xlog_keep_days=30
#성능 카운터 정보를 유지하는 기간
mgr_purge_counter_keep_days=70
```

