# ELK

 ElasticSearch관련 연구과제를 서치하던 도중 ELK의 개념이 잘 정리되어 있는 블로그가 있어서 정리해본다.



# ELK 란?

![image](https://user-images.githubusercontent.com/54675591/134859093-b6d69535-d297-4293-9aee-0eb3cb90118a.png)

 ELK는 위 그림과 같이, 분석 및 저장 기능을 담당하는 ElasticSearch, 수집 기능을 하는 Logstash, 이를 시각화하는 도구인 Kibana의 앞글자만 딴 단어이다. ELK는 접근성과 용이성이 좋아 최근 가장 핫한 Log 및 데이터 분석 도구이다.

### 1. ElasticSearch

* ElasticSearch는 Lucene 기반으로 개발한 분산 검색엔지으로, Logstash를 통해 수신된 데이터를 저장소에 저장하는 역할을 담당한다.
* 데이터를 중심부에 저장하여 예상되는 항목을 검색하고 예상치 못한 항목을 밝혀낼 수 있다.
* 정형, 비정형, 위치정보, 메트릭 등 원하는 방법으로 다양한 유형의 검색을 수행하고 결합할 수 있다.
* 엘라스틱서치(Elasticsearch)는 기본적으로 검색 엔진이다. 전통적인 관계형 데이터베이스의 잣대로 보면 아래와 같은 장점과 단점이 나타난다. 검색 엔진의 특성을 정확하게 이해하고 사용한다.

### 2. Logstash

* 오픈소스 서버측 데이터 처리 파이프라인으로, 다양한 소스에서 동시에 데이터를 수집하고 변환하여 stash 보관소로 보낸다.
* 수집할 로그를 선정해서 지정된 대상 서버(ElasticSearch)에 인덱싱하여 전송하는 소프트웨어이다.

### 3. Kibana

* 데이터를 시각적으로 탐색하고 실시간으로 분석 할 수 있다.
* 시각화를 담당하는 HTML + Javascript 엔진이라고 보면 된다.
* 키바나(Kibana)의 경우 대체 가능한 소프트웨어로 그라파나(Grafana)가 있다. 데이터베이스 관리자가 주로 사용할 법한 기능을 일부 제외하고 UI측면을 강화한 특징이 있다.



# ELK Stack

![image](https://user-images.githubusercontent.com/54675591/134859956-efbd99e0-947c-40ac-a141-a1fc8db01929.png)

ELK 솔루션에서 Beats가 추가되면서 ELK Stack이라고 불린다.

> Beats
>
> 서버에 에이전트로 설치하여 다양한 유형의 데이터를 ElasticSearch 또는 Logstash에 전송하는 오픈 소스 데이터 발송자다.



# 장단점

### 장점

* 강력한 유연성과 호환성

  Elasticsearch, Logstash, Kibana는 각각 데이터의 검색, 수집, 시각화를 담당한다. 용도별로 분리하여 발전하는 솔루션이기에 구조적 안정성은 물론 다른 시스템과도 유연한 호환성을 가진다.

* 자유 스키마

  JSON 방식의 Key-Value 형식의 데이터를 사용하므로 형식에 자유롭다. 인덱스의 Union, Join이 경우에 따라 와일드 카드 표기로 끝낼 수 있다.(여기서 인덱스는 Table에 해당한다.)

* 확장(Scale-out) 가능 데이터베이스

  처음부터 확장을 고려하여 만들어졌기 때문에 여러대의 서버를 엮어서 성능 향상을 기대할 수 있는 클러스터 방식을 구성할 때 관계형 데이터베이스보다 상대적으로 관련 정보에 쉽게 접근이 가능하다

* 데이터 처리 절차를 '레거시 코딩'보단 개별 설정으로 가능

  데이터 처리 절차를 프로그래밍 언어를 이용한 코딩으로 명시한다면, 향후 유지보수가 용이하지 않고 불필요한 종속성이 높아지는 문제가 있지만  Logstash를 포함하는 ELK Stack 구성 시 유연성 확보가 가능하다.

* 사전에 준비된 시각화 도구와 부가기능

  데이터를 시각화하여 보여주기 위한 UI를 내볼낼 프로그램을 따로 작성하지 않아도, 이미 사전에 준비된 시각화 도국를 가진 Kibaba가 마우스 조작으로 거진 다 알아서 해주낟. 그 외 데이터베이스 관리자를 위한 부가기능이 포함되어 있다.

* 실시간 데이터 처리

  Mesaage Queue(이하 MQ)와 결합하면 강력한 실시간 데이터 수집 및 처리 시스템이 된다.

* 엘라스틱서치의 버전 별 벌크(Bulk) 방법 차이를 완화

  엘라스틱서치의 벌크(Bulk)는 관계형 데이터베이스에서 삽입(Insert)에 해당한다. 프로그래밍으로 직접 구현할 경우 엘라스틱서치 버전 별 벌크 방법의 차이가 크기 때문에, 인터넷에 떠돌아다니는 튜토리얼만 보고 진행하기에는 오래된 자료도 많아 무리가 따를 수 있다. 로그스태시로 엘라스틱서치를 다룬다면 이런 걱정은 하지 않아도 된다.

### 단점

* 초기 데이터 구성 및 이관 문제

  빠른 데이터 처리를 장점으로 표방하고 있으나, 초기 데이터 구성이 기존에 보유한 데이터를 엘라스틱서치로 이관하는 것에서 시작하는 경우가 많음에도 공식적으로 공개된 튜토리얼은 심각한 성능 저하를 일으킬 수 있는 내용이 주를 이루고 있다. 문제는 대부분 실시간 데이터를 처리하는데 적합한 방식을 대용량 데이터 이관에도 유용하다고 소개하는 점에서 비롯된다. 가령, MQ를 이용하면 실시간 데이터 처리를 분명 안정성과 속도면에서 강점이 있으나, 100GB 이상의 관계형 데이터베이스의 데이터 이관 절차에 그대로 적용하면 상당한 성능 저하의 우려가 있다. JDBC를 이용한 방법도 공식 튜토리얼이나 지나친 메모리 누수 문제로 인해 데이터가 몇 기가 정도면 되어도 사실상 데이터 이관 용도로는 사용이 불가능하다. 하지만 해결책은 있다. 데이터 이관이 목적인 경우, 데이터베이스 테이블 내용 전체를 파일로 저장한 다음 파일비트(Filebeat)를 쓰는게 훨씬 빠를 수 있다. 기존 데이터의 용량이 너무 커서 어쩔 수 없이 네트워크를 이용해도 로그스태시의 접점에서 속도 저하 및 메모리 누수를 최소화할 수 있도록, 목표한 파일이나 데이터베이스 테이블의 행을 하나 이상 씩 일정한 수로 읽으면서 TCP 또는 UDP 등 소켓을 기반으로 데이터를 전송하는 프로그램을 직접 구현하면 해결이 가능하다.

* 커널 변수의 불필요한 사용

  ELK 스택을 구성하는 솔루션 중 로그스태시는 리눅스 운영체제 커널의 cgroups에 해당하는 변수를 참조하는 내용이 포함되어 있는데, 이것은 운영체제에서 정한 임계값을 파악하기 위함이다. 하지만, 커널 컴파일 설정에 따라 cgroups 내의 변수의 일부는 존재하지 않을 수 있으에도, 데이터 수집이라는 목적에 변수가 존재하지 않는 이유로 프로그램이 중단되는 문제점이 있다. 주로 운영체제의 커널 크기를 최소화하려는 시도가 반영되었거나, 커널 공유 방식의 가상화를 진행하는 환경에서 문제가 발생하여 정상 설치 리눅스 운영체제의 경우 문제가 발생하지 않는다.

* 초창기부터 현재까지도 원활하지 못한 시간대(Timezone)처리

  3개의 스택(로그스태시->엘라스틱서치->키바나)로 데이터가 넘어가는 과정에서 시간대 일관성이 깨지는 문제가 있다. 'Asia/Seoul'같이 명시를 하여도 마치 2중 환전을 연상케하는 시간대 변환 오류를 쉽게 범한다. 하지만 3개 솔루션 모두 기본 시간대가 UTC라는 점을 인지하고, 시간대 설정이 가능한 경우 일관성을 유지하려는 노력으로 극복이 가능하다.



[출처](https://velog.io/@courage331/ELK)
