# 사용자 정의 알림 설정하기

Scouter에서 강조하는 것 중 하나가 "scouter는 개발자가 가장 잘 활용 할 수 있는 APM이다"인데, 그 이유가 압축 해제만으로 설치가 끝나고 소규모 장비의 일부 지원으로도 잘 돌아간다는 점도 있지만 가장 중요하게 생각하는 ㅂ분은 바로 개발자에게 주어진 자유도입니다.

특히 스크립트 방식의 플러그인을 통하면 개발자가 원하는 정보를 소스의 수정없이 scouter의 프로파일이나 xlog에 남길 수 있는데요, 이번 장에서는 이와 유사한 방식으로 스크립트를 통해 자유도 높은 알림 설정할 수 있는 기능을 소개하고자 합니다. (스크립트 방식의 플러그인 활용에 대해서는 다음에 다른 지면을 통해 다뤄보도록 하겠습니다.)

---

**1. 알림 플러그인 설치**

Scouter의 알림은 클라이언트 화면에서도 볼 수 있지만 플러그인을 설치하여 이메일이나 메신저로 확인하는 것이 좋습니다. Scouter는 email, slack, line메신저 등의 확장 플러그인을 가지고 있으며 scoute-project의 github페이지에서 확인할 수 있습니다.

확장 플러그인 설치는 아래 순서로 진행합니다.

* 플러그인 릴리즈 제공하는 파일들을 모두 다운로드 받는다
  * 예를 들어 slack 플러그인을 설치하려면 scouter-server-plugin-alert-slack의 릴리즈 페이지에 있는 파일을 모두 다운로드 받는다.
    * https://github.com/scouter-project/scouter-plugin-server-alert-slack/releases/tag/v1.0.0
* scouter controller server가 설치된 디렉토리 아래의 lib 디렉토리로 받은 파일들을 모두 복사한다.
* scouter collector를 재기동한다.

> 설치된 plugin이 제대로 적용되는지 collector 시작시 남는 로그를 통해 확인할 수 있습니다.

---

**2. 기본 제공 알림**

Scouter에서는 간편하게 설정할 수 있는 몇가지 기본 알림을 제공합니다.

* CPU알림 설정 옵션

  * Host agent의 옵션을 통해 설정할 수 있습니다.

  * cpu_alert_enabled :  cpu알림을 활성화하거나 비활성화 합니다.(기본:true)

  * cpu_warning_pct : cpu warning 레벨 알림의 퍼센트입니다.(정수로 설정)

  * cpu_fatal_pct : cpu fatal 레벨 알림의 퍼센트입니다. (정수로 설정)

  * cpu_check_peried_ms : 어느 기간동안 cpu를 체크할지를 지정합니다.

  * cpu_waring_history, cpu_fatal_history : 각 레벨에 설정한 값의 초과가 몇회 발생하였을때 알림을 알릴지를 설정합니다.

  * - 예를 들어 *cpu_fatal_pct*가 90%이고 *cpu_check_period_ms*가 5분(300000ms), *cpu_fatal_history*가 3이라면 CPU가 최근 5분간 90%를 3회 넘는 경우 알림을 발송하게 됩니다.

  * cpu_alert_interval_ms : 설정 시간동안 동일한 알림을 발송하지 않습니다. 

*  Memory 알림 설정 옵션

  * mem_alert_enabled=false
  * mem_alert_interval_ms=30000
  * mem_warning_pct=80
  * mem_fatal_pct=90

* Disk 사용량 알림 설정 옵션

  * disk_alert_enabled=true
  * disk_warning_pct=70
  * disk_fatal_pct=90

---

**3. 알림 스크립팅을 통한 고급 알림 제어**

Java 언어를 사용한 스크립팅을 통해 복합적인 조건으로 알림을 설정할 수 있습니다.

알림 스크립팅은 클라이언트 object view에서 collector의 컨텍스트 메뉴를 통해서 접근이 가능합니다. 아래 그림에서 Customizable Alert 메뉴를 보면 여러가지 항목이 보이는데 이는 scouter로 전송되는 카운터들을 나타냅니다. 알림은 모든 카운터에 대해 설정할 수 있으며 또한 이를 조합하여 사용할 수도 있습니다.

![img](https://3.bp.blogspot.com/-L6L7j_Fk7Lc/WwPKtRYyXPI/AAAAAAAAd1w/QEM0POPWdr4poz3WWWN2UDyc5Bn8n43-wCLcBGAs/s400/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-22%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B4.45.22.png)

<p align="center"><그림.Scouter의 알림 스크립팅 메뉴></p>

그러면 이해를 돕기 위해 응답시간에 대한 알림을 설정해 보도록 하겠습니다. 위 메뉴에서 Elapsed90% 항목을 선택합니다. 이러면 아래와 같은 화면이 열립니다.

> Elapsed90% 카운터는 상위90%의 응답시간입니다. 평균 응답시간은 이상 현상을 발견하는데 민감성이 떨어지므로 scouter에는 90% 응답시간 카운터도 제공하고 있습니다.

![img](https://3.bp.blogspot.com/-CWwxLQYJiWo/WwPMhUQ2b8I/AAAAAAAAd18/62sEI6AONbIQNhRHIv-urhcR63cc6xCtACLcBGAs/s640/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-22%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B4.53.15.png)

<p align="center"><그림. Scouter의 알림 스크립팅 화면></p>

왼쪽은 스크립트를 작성하는 영역, 오른쪽은 스크립트에 전달된 두개의 파라미터 `$counter`와 `$$`에 대한 도움말 화면입니다. 그리고 왼쪽 아래 영역은 이 알림에 대한 체크 주기등에 대한 설정이고 오른쪽 아래 영역은 알림 스크립트를 저장했을 때 제대로 컴파일이 되는지 등을 알려주는 콘솔창입니다.

먼저 왼쪽 아래 영역의 **"Alert Configuration"**에서는 알림을 어떤 주기로 체크하고 이 값을 몇개나 보관할지를 설정합니다.

* history_size = 150
  * scouter의 카운터 값들은 2초에 하나씩 수집이 됩니다. 즉 여기에 150이라고 설정된 부분은 최근 150개까지 보관하라는 것이며 결국 과거 300초의 데이터가 보관이 됩니다.
* slient_time=300
  * 초단위로 설정하는 snooze 시간입니다. 즉 한번 발생한 알림은 설정된 값이 300초간 다시 발송하지 않습니다.
* check_time=2
  * 초단위의 값입니다. 2초 간격으로 위 알림 스크립트를 실행하라는 설정입니다. 측정하고자 하는 값의 성격에 따라 적절히 조절하면 됩니다.

자 이제 알림 스크립팅을 해보도록 하겠습니다. 파라미터로 전달되는 두가지 오브젝트를 적절히 활용하여 스크립팅을 하게 됩니다.

* $counter : 카운터의 값을 얻어오거나 알림을 발송할 수 있습니다.
* $$ : 스크립팅에 필요한 몇가지 유틸리티들을 제공하는 오브젝트입니다.

스크립팅은 Java 문법을 사용하는데 몇가지 제약 사항이 있습니다.

* import 구문을 사용할 수 없으므로 java.lang에 포함된 클래스 외에는 패키지명을 포함하여 선언하여야 합니다.
* varargs를 사용할 수 없습니다. varargs를 받는 메소드에 파라미터를 전달해야 한다면 배열로 만들어 전달하여야 합니다.

일단 아래 조건으로 알림을 발송해보겠습니다.

* 90% 응답시간이 2000ms를 넘는 경우 fatal알림을 발송한다.

```java
float value = $counter.getFloatValue();

if(value > 2000){
    $counter.fatal("Alert title","msg: Elapsed is over 2000ms");
}
```

그러면 Alert View에 알림이 뜨게 된다.

![scouter26](https://user-images.githubusercontent.com/54675591/121142005-8780e680-c876-11eb-9ef0-19918ded662e.PNG)

동작을 확인하였스인 알림 발송 기준에 몇 가지 조건을 더 추가하여 개선해 보도록 하겠습니다.

* 알림 메시지에 현재 값과 objName과 objType정보를 추가로 기록한다.
* 야간 등 트래픽이 적을때는 응답시간이 늦은 한 두개의 요청 만으로도 알림이 발생할 수 있다. 따라서 TPS가 10미만인 경우는 알림을 발생시키지 않도록 한다.
* 알림 메시지에 현재 TPS도 기록한다.
  * $counter.getFloatValue(String counterName)메소드로 다른 카운터 항목도 가져올 수 있습니다. TPS값을 가져오려면 $counter.getFloatValue("TPS")를 사용하면 됩니다.

> counterName은 scounter client에서 해당 counter차트를 선택하면 아래쪽 status bar에 표시됩니다.

```java
float value = $counter.getFloatValue();

if(value>2000){
    $counter.fatal("Alert title", "msg : Elapsed is over 2000ms");
}
```

위 코드를 저장하고 응답시가닝 2000ms를 넘게되면 클라이언트 화면에서 아래와 같은 알림을 확인할 수 있습니다. 보통은 화면에서 확인하기 보다는 알림이 연동된 메시지 등을 통해 내용을 확인하게 됩니다.

![img](https://3.bp.blogspot.com/-f5wUGYJfDzA/WwStaT-O4GI/AAAAAAAAd2I/R1atf6wkTNAahrjqm-KJjJequ5QSK6YuACLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-23%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB%2B8.52.45.png)

<p align="center"><그림. Scouter 알림창></p>

동작을 확인하였으니 알림 방송 기준에 몇가지 조건을 더 추가하여 개선해 보도록 하겠습니다.

* 알림 메시지에 현재 값과 objName과 objType 정보를 추가로 기록한다.
* 야간 등 트래픽이 적을때는 응답시간이 늦은 한 두개의 요청만으로도 알림이 발생할 수 있다. 따라서 TPS가 10미만인 경우는 알림을 발생시키지 않도록 한다.
* 알림 메시지에 현재 TPS도 기록한다.
  * **$counter.getFloatValue(String counterName)** 메소드로 다른 카운터 항목도 가져올 수 있습니다. TPS 값을 가져오려면 **$counter.getFloatValue("TPS")** 를 사용하면 됩니다.

> counterName은 scouter client에서 해당 counter 차트를 선택하면 아래쪽 status bar에 표시됩니다.

```java
String objType = $counter.getObjType();
String objName = $counter.getObjName();
float value = $counter.getFloatValue();
float tps = $counter.getFloatValue("TPS");

String title = "Elapsed time alert";
String message = "Elapsed is over 2000ms\n";
message += "[current value] " + value + "ms\n";
message += "[objType] " + objType + "\n";
message += "[objName] " + objName + "\n";
message += "[TPS] " + tps;

if(value > 2000 && tps > 10.0) {
	$counter.fatal(title, message);
}
```

![img](https://1.bp.blogspot.com/-mLZ5HqEVLGs/WwS1wCzg-WI/AAAAAAAAd2U/rN7zgyA_X3sDqOC085Ge8OS2V14MV_pKACLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-23%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%258C%25E1%2585%25A5%25E1%2586%25AB%2B9.28.30.png)

<p align="center"><그림.조건이 추가된 Scouter 알림창></p>

소수점까지 보이는 숫자가 조금 보기 좋지 않으니 $$.formatNumber() 메소드를 사용해서 조금만 더 수정해보겠습니다.

```java
String objType = $counter.getObjType();
String objName = $counter.getObjName();
float value = $counter.getFloatValue();
float tps = $counter.getFloatValue("TPS");

String title = "Elapsed time alert";
String message = "Elapsed is over 2,000ms\n";
message += "[current value] " + $$.formatNumber(value, 0) + "ms\n";
message += "[objType] " + objType + "\n";
message += "[objName] " + objName + "\n";
message += "[TPS] " + $$.formatNumber(tps);

if(value > 2000 && tps > 10.0) {
	$counter.fatal(title, message);
}
```

만약 컴파일 에러가 발생하면 Console창에 ***Alert  rule compile error***라고 표시되면 정상적으로 컴파일 되어 로드되면 ***Alert rule detected***라고 표시됩니다.

![img](https://3.bp.blogspot.com/-FouTcHF9LFY/WwVi9HYKzfI/AAAAAAAAd2g/-txAXWbbJS8ULlE1CuadexIxQaFL_Nl3wCLcBGAs/s400/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-23%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B9.47.26.png)

<p align="center"><그림.Console></p>

counter 값을 과거의 데이터와 비교해야 하는 경우도 있습니다. 이런 경우에는 $counter.getAvg(int fromAogSec, int durationSec)를 사용합니다.

예를 들어 아래와 같은 요건으로 알리을 작성하고자 한다면 이렇게 작성합니다. Customizable alert의 TPS 메뉴를 열어 작성한 스크립트 예시입니다.

* 만약 과거 3분 이전의 30초 평균 TPS보다 현재 시점의 30초 평균 TPS가 1.5배 높다면 fatal 알림을 발송한다.

```java
String objType = $counter.getObjType();
String objName = $counter.getObjName();
float avgTpsCurrent = $counter.getLatestAvg(30);	//latest 30s avg
float avgTps3minAgo = $counter.getAvg(180+30,30);

String title = "TPS fluctuation alert";
String message = "TPS is highly(over 1.5x) incresed in 3min\n";
message += "[TPS current] " + $$.formatNumber(avgTpsCurrent) + "\n";
message += "[TPS 3min ago]" + $$.formatNumber(avgTps3minAgo) + "\n";
message += "[objType] " + objType + "\n";
message += "[objName] " + objName + "\n";

if(avgTpsCurrent > avgTps3minAgo*1.5){
    $counter.fatal(title, message);
}
```

알림 설정은 한번에 끝내는 것이 아니고 모니터링 하면서 민감도를 지속적으로 조절해야 합니다.

너무 의미없이 자주 오는 알림은 아무도 보지 않고, 그렇다고 중요한 시점에 알람이 발송되는 것을 놓쳐서도 안됩니다. 지금까지 소개한 알림 스크립팅을 활용한다면 다양한 카운터의 값들과 조건을 종합적으로 판단하여 최적화된 알림을 발송할 수 있습니다.

마지막으로 알림에 간단한 차트 링크를 첨부하는 예제입니다.

scouter webapi에 간단한 web chart 기능이 있습니다. 이 web chart를 링크로 제공하는 예제이며, 조금 복잡하지만 차근히 보시면 이해가 될 겁니다.

알림 메시지에 알림이 발생한 시점의 차트, 현재 시점의 차트, 그리고 일일 차트에 대한 링크를 넣어보도록 하겠습니다. 여기서는 일단 webapi server를 따로 띄우지 않고 collector에 embedded된 상태로 사용하겠습니다. 이를 위해서는 collector에 net_http_server_enable과 net_http_api_enabled옵션이 true가 되어 있어야 합니다. 이를 설정하고 collector를 재기동 하면 6180port를 통해 webapi에 접근할 수 있습니다.

```java
String counterName = "Elapsed90%";
String objType = $counter.getObjType();
String objName = $counter.getObjName();
float value = $counter.getFloatValue();
float tps = $counter.getFloatValue("TPS");

String widgetUrl = "http://my-scouter-webapi-ip:6180/widget/simple/counter.html?source=";

java.text.SimpleDateFormat dateFormat = new java.text.SimpleDateFormat("yyyyMMdd");
long now = System.currentTimeMillis();
String today = dateFormat.format(new java.util.Date(now));
String yesterday = dateFormat.format(new java.util.Date(now-24*60*60*1000));

String counterApi = counterName + "/ofType/" + objType;
counterApi += "?startTimeMillis=" + (now-600*1000L);
counterApi += "&endTimeMillis=" + now;

String counterApiLatest = counterName + "/latest/600/ofType/" + objType;

String dayStatApi = "stat/" + counterName + "/ofType/" + objType;
dayStatApi += "?startYmd=" + yesterday;
dayStatApi += "&endYmd=" + today;

String title = "Elapsed time alert";
String message = "Elapsed is over 2,000ms\n";
message += "[current value] " + $$.formatNumber(value, 0) + "ms\n";
message += "[objType] " + objType + "\n";
message += "[objName] " + objName + "\n";
message += "[TPS] " + $$.formatNumber(tps) + "\n";

message += "[On-time chart]\n";
message += widgetUrl + java.net.URLEncoder.encode(counterApi) + "\n";
message += "[Current chart]\n";
message += widgetUrl + java.net.URLEncoder.encode(counterApiLatest) + "\n";
message += "[Daily chart]\n";
message += widgetUrl + java.net.URLEncoder.encode(dayStatApi);

if(value > 2000 && tps > 10.0) {
	$counter.fatal(title, message);
}
```

알림이 발생하면 다음과 같은 알림 메시지를 받게 됩니다.

![img](https://4.bp.blogspot.com/-Wx0-XY4yKBo/WwVuVqPWNGI/AAAAAAAAd2w/7uruk6z00d8DbRHcuxH3Ti7DB17mUA1jwCLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-23%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B10.36.01.png)

이제 알림으로 전송된 메시지의 링크를 누르면 아래와 같은 차트를 볼 수 있습니다.

![img](https://2.bp.blogspot.com/-g37QAshCxqo/WwVuGW4fUVI/AAAAAAAAd2s/oXDwzqCFnlIpdGpC0EQim7Ta3XT31AoYACLcBGAs/s320/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA%2B2018-05-23%2B%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE%2B10.34.42.png)

사실scouter-paper 같은 3rd party UI를 알림과 연결하는 것이 더 고급스럽고 활용도가 높습니다.

