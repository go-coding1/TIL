# Quartz 

Quartz는 오픈 소스 작업 스케줄링 프레임워크이다. Quartz는 완전히 자바로 작성되어 잇으며, J2SE와 J2EE 어플리케이션 모두에서 사용될 목적으로 설계되었다. Quartz는 매우 유연하며 단순한 구조를 제공한다. 간단한 작업은 물론 복잡한 작업 모두에 대한 스케줄링을 작성할 수 있다 Quartz는 또한 EJB, JavaMail 등을 위한 데이터베이스 지원, 클러스터링, 플러그 인, 미리 내장된 작업들을 포함하고 있으며, cron과 유사한 표현식도 지원한다.

### The Quartz Scheduler

---

Quartz 프레임워크의 핵심은 Scheduler 이다. Scheduler는 Quartz 어플리케이션을 위한 런타임 환경을 관리하는 책임을 지니고 있다. Scheduler 그 자체가 모든 작업을 수행하는 것은 아니다. Scheduler는 프레임워크 내부에 있는 매우 중요한 몇몇 컴포넌트들에 그 작업을 의존하고 있다. 확장성을 보장하기 위해, Quartz는 멀티스레드 아키텍처를 기반으로 하고 있다. Quartz 프레임워크가 시작될 때, 프레임워크는 스케줄링 된 Job들을 실행하기 위해 Scheduler가 사용하는 "worker 스레드들"을 초기화 한다. Quartz가 동시에 수많은 Job들을 실행 가능한 이유가 바로 여기에 있다. Quartz는 스레드 환경을 관리하기 위해 ThreadPool 관리 컴포넌트들에 의존하고 있는데, 그 결합도는 느슨하다. 이 글을 통해 여러번 언급할 것이지만, 이는 Quartz에 있는 모든 것들을 환경설정이 가능하거나 사용자가 정의해 지정할 수 있음을 의미한다. 예를 들면, 여러분이 정의한 "ThreadPool" 관리 기능을 플러그 인 형태로 끼워 넣고 싶은 경우, 이러한 작업이 가능하다.

### Jobs, Jobs and More Jobs

---

Quartz의 용어를 사용하자면, Job은 작업을 수행하는 간단한 자바 클래스이다. 이 작업은 자바에서 코드로 작성 가능한 그 어더한 것이라도 될 수 있다. 필수적으로 요구되는 사항은, `org.quartz.Job`인터페이스를 구현하고, 심각한 오류 발생시 `JobExecutionException`을 발생시키는 것뿐이다. 여러분은 이미 앞에서 Job 인터페이스와 포함된 `execute()`메소드를 보았다.

`Job Interface`와 `execute()`메소드를 구현했다면, Quartz는 Job 실행 시기가 되었을 때, Job을 호출한다. `execute()`메소드 안에서 무엇을 수행하는가는 전적으로 개발자에게 달려 있다. 다음은 Job 내부에서 실행할 작업들에 대한 몇몇 예이다.

* JavaMail이나 Commons Net과 같은 다른 메일 프레임워크를 사용하여 전자메일 저송
* EJB에 대한 원격 인터페이스를 생헌한 후, 이 인터페이스의 메소드 호출
* Hibernate 세션을 얻어 관계형 데이터베이스에 있는 데이터 질의와 갱신
* OSWorkflow를 사용하여 Job으로 워크플로우 호출
* FTP를 사용해 파일 옮기기
* Ant 빌드 스크립트를 호출해 스케줄링 되어 있는 빌드 작업 시작

수많은 여러 작업들이 가능하며, 이것이 바로 Quartz 프레임워크를 매우 강력하게 만들어주는 이유이다. Quartz는 매우 일반적이고 반복적인 스케줄을 작성해주는 메커니즘을 제공하기 때문에, 개발자는 단지 실행을 위해 호출될 자바 클래스들만 작성하면 된다.

### Available Job Stores 2가지

---

Quartz 프레임워크에서는 두 가지 기본적인 JobStore 타입을 제공한다.

- **RAMJobStore** : Scheduler 정보를 유지하는데 일반적인 메모리(RAM)을 사용하는 첫 번째 타입은 RAMJobStore라 불린다. 이러한 타입의 JobStore는 설정 및 실행이 매우 간단하다. 많은 어플리케이션들에 대해 이러한 JobStore만으로도 충분할 것이다. 그러나, Scheduler 정보가 JVM에 할당되어 있는 메모리에 저장되기 때문에, 어플리케이션이 멈추게 되면, 스케쥴과 관련된 모든 정보가 사라진다.

- **JDBC Store** : 두번째 타입의 JobStore는 실제로 Quartz 프레임워크에서 두 가지의 서로 다른 형태로 구현되어 제공되고 있지만, 이둘 모두 일반적으로 JDBC JobStore로 일컬어지고 있다. 이 두 가지 모두의 JDBC JobStore는 JDBC 드라이버를 사용하며, 스케줄 정보를 유지하고 있는 관계형 데이터베이스로부터 정보를 가져온다. 이 두가지 타입은 데이터베이스 트랜잭션을 제어하는지의 여부나 BEA의 WebLogic이나 JBoss와 같은 어플리케이션 컨테이너에 제어를 넘기는지의 여부에 그 차이점이 존재한다.

  두 가지 유형의 JDBC JobStore는 다음과 같다.

  * JobStoreTX : 트랜잭션을 제어하고 싶은 경우나, 서버 환경 없이 어플리케이션을 운영하려 할 때 사용된다.
  * JobStoreCMT : 어플리케이션 서버 환경 내에서 어플리케이션이 운영되며 컨테이너가 트랜잭션을 관리하도록 하고 싶은 경우 사용된다.

JDBC JobStore는 어플리케이션이 중지되고 다시 시작된 후에라도 스케줄링 정보를 유지하여 Scheduler가 실행되도록 만들어야 할 경우를 위해 설계되었다.



### Job and Triggers

---

Quartz 설계자들은 Job과 스케줄을 분리하였다. Quartz에서 Trigger는 Job이 트리거링 되거나 발생되어야 할 때, Sheduler에게 알려주는데 사용된다. Quartz 프레임워크에서는 간단한 Trigger 타입들을 제공하고 있는데, Simple Trigger와 CronTrigger가 가장 일반적으로 사용된다.

Simple Trigger는 스케줄을 간단히 발생시키는데 사용도리 목적으로 설계되었다. 일반적으로, 주어진 시간에 Job을 발생시켜 (m)초 간격을 두고 여러 번(n) 이를 실행할 필요가 있을 경우, Simple Trigger가 적합한 선택이 된다. 반면, Job에 요구되는 스케줄링이 복잡할 경우, CronTrigger가 적합할 것이다. 

CronTrigger는 달력과 유사하 스케줄에 기반하고 있다. 만약 여러분의 Job이 매주 토요일과 일요일은 제외한, 매일 오전 10시 30분마다 실행되어야 하는 경우에, CronTrigger가 사용된다. 이 이름이 암시하고 있듯, CronTrigger는 Unix의 cron 표현식을 기반으로 하고 있다. 예를 들면, 다음의 Quartz cron 표현식은 월요일부터 금요일에 걸쳐 매일 오전 10시 15분에 Job을 실행할 것이다.

```cron
0 15 10 ? * MON-FRI
```

그리고 다음 표현식은 2002, 2003, 2004, 2005년 동안 매월 마지막 금요일 오후 10시 15분에 Job을 실행할 것이다.

```cron
0 15 10 ? * 6L 2002-2005
```

이러한 작업은 Simple Trigger로 수행될 수 없다. 이 둘 모두 Job에 사용될 수 있다. 어떤한 것을 사용할 지에 관한 선택은 스케줄링 될 작업 성격에 달려 있다.



### Listener and Plugins

---

오늘날 어떠한 오픈 소스 프레임워크라도 사용하고 있는 개념이 바로 이 둘이다.

Quartz Listener는 주요 이벤트가 발생할 때, 프레임워크 내부로부터 콜백을 받는 자바 클래스이다. 예를 들면, Job이 스케줄 되어 있거나 스케줄 되어 있지 않을 때, 또는 Trigger가 끝났거나, 더 이상 발생시키지 않을 때, 이러한 모든 것들은 Listener로 통지되도록 설정될 수 있다. Quartz 프레임워크는 Schedular, Job, Trigger들을 위한 Listener들을 포함하고 있다. 또한, Job Listener와 Trigger Listener들을 특정한 한 Job이나 Trigger에 적용되도록 만들거나 전체에 걸쳐 적용되도록 설정할 수도 있다.

일단 Listener가 호출되면, 이 정보를 사용해 Listener 클래스 내에서 수행하려는 어떠한 로직이라도 실행시킬 수 있다. 예를 들면, 만약 Job이 완료될 때마다 전자 메일을 보내고 싶은 경우, 이를 Job에 프로그래밍 해 넣을 수 있다. 또한, JobListener를 사용할 수도 있다.

Quartz Plugin은 Quartz 소스를 수정하지 않고도 Quartz 프레임워크에 기능을 추가시켜주는 새로운 기능이다. 이것은 Quartz 프레임워크를 확장해야 하는데, 변경한 기능을 Quartz 개발 팀에게 보내주고 다음 버전에 반영되기까지 기다릴 시간이 없는 개발자들을 위한 기능이다. 여러분이 Struts플러그 인에 익숙하다면, Quartz 플러그 인을 사용하는 방법을 더 쉽게 이해할 수 있을 것이다.

### Clustering Quartz Applications

---

> **클러스터링(군집화)** | cluster : 군집, 무리, 무리를 이루다, 모이다
>
> 클러스터링은 개체들이 주어졌을 때, 개체들을 몇 개의 클러스터(부분 그룹)으로 나누는 과정을 의미합니다. 이렇게 개체들을 그룹으로 나누는 과정을 통해서, 클러스터 내부 멤버들 사이는 서로 가깝거나 비슷하게 서로 다른 두 클러스터 사이의 멤버간에는 서로 멀거나 비슷하지 않게 하는 것이 클러스터링의 목표입니다.

Quartz 어플리케이션은 여러분의 요구 사항에 따라 수직/수평적으로 모두 클러스터링 될 수 있다. 클러스터링을 통해 다른 클러스터링 타입과 마찬가지의 이점들을 제공받을 수 있다.

* 확장성
* 높은 가용성
* 로드 밸런싱

현재 Quartz는 관계형 데이터베이스와 JDBC JobStore 중 하나의 도움을 통해 클러스터링을 지원한다. 향후 버전에서는, 이러한 제한이 사라져, 데이터베이스 없이도 RAMJobStore에서 사용 가능해질 것이다.



### Spring에서의 Quartz예시

---

pom.xml

```xml
<!--Scheduling-->
<dependency>
	<groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version2.1.7</dependency>
    <exclusions>
    	<exclusion>
        	<artifactId>slf4j-api</artifactId>
            <groupId>org.slf4j</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
	<groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-jobs</artifactId>
    <versions>2.2.1</versions>
</dependency>
<dependency>
	<groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz-oracle</artifactId>
    <version>2.1.7</version>
</dependency>
<!-- Scheduling end -->
```

spring 설정

```xml
<!-- 스케쥴러 생성 -->
<bean id="quartzScheduler"
      class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="transactionManager" ref="txManager" />
    <property name="configLocation"
              value="classpath:/egovframework/egovProps/quartz.properties" />
</bean>

<bean id="JobConfScheduler" 	
      class="com.mindone.water.batchprocess.service.JobConfScheduler" 
      init-method="init" 
      destory-method="destory">
</bean>
```

quartz.properties

```properties
org.quartz.scheduler.instanceName = ClusteredScheduler
org.quartz.scheduler.instanceId = AUTO
org.quartz.scheduler.jobFactory.class = org.quartz.simpl.SimpleJobFactory

org.quartz.threadPool.class = org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount = 10
org.quartz.threadPool.threadPriority = 5
org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread = true

org.quartz.jobStore.misfireThreshold = 60000
org.quartz.jobStore.class = org.quratz.impl.jdbcjobstore.JobStoreCMT
org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate
org.quartz.jobStore.useProperties = false
org.quartz.jobStore.tablePrefix = QZ_
org.quartz.jobStore.isClustered = true
org.quartz.jobStore.clusterCheckinInterval = 20000
```

java

```java
public class BatchFacilityErrorSendSmsScheduling implements Job{
    /* logger */
    private static final Logger LOGGER = LoggerFactory.getLogger(BatchFacilityErrorSendSmsScheduling.class);
    
    @Override
    public void execute(JobExectionContext jobContext) throws JobExecutionException{
        //LOGGER.info("job Trigger이름 : [{}]", jobContext,getJobDetail().getKey().getName(), jobContext.getTrigger().getKey().getName());
        
        JobDataMap dataMap = jobContext.getJobDetail().getJobDataMap();
        
        ManageUserService manageUserService = (ManageUserService) SpringContextUtil.getBean("manageUserService");
		SystemMtrConfService systemMtrConfService = (SystemMtrConfService) SpringContextUtil.getBean("systemMtrConfService");
		SystemMtrHistService systemMtrHistService = (SystemMtrHistService) SpringContextUtil.getBean("systemMtrHistService");
		RwisService rwisService = (RwisService) SpringContextUtil.getBean("rwisService");

		int jobResult = 0;
		
		try {
			String fireTime = DateStringUtil.getDateToFormatString(jobContext.getFireTime(), "yyyyMMddHHmm");
			//LOGGER.info("job Trigger이름 : [{}]", jobContext.getJobDetail().getKey().getName(), jobContext.getTrigger().getKey().getName(), fireTime);
			
			if(jobContext.getResult()!= null){
				jobResult = (Integer) jobContext.getResult();
			}
			//메일발송 담당자 조회
			Map<String, Object> param = new HashMap<String, Object>();
			param.put("searchType", "smsYn");
			param.put("keyWord", "Y");
			param.put("authSn", "AU00000001");
			List<HashMap<String, Object>> userList = manageUserService.searchUserList(param);
			
			//상위 컴퓨터 설비 이상 체크(ping test)
			systemMtrHistService.ipCheck();
			
			//상위 컴퓨터 설비 문자발송
			systemMtrHistService.ipSendSms(userList);
			
			//현장 제어반 문자발송
			systemMtrHistService.fcltySendSms(userList);
			
		} catch(Exception e){
			LoggerUtil lu = new LoggerUtil(e);
			dataMap.put("errDesc", lu.print());
			LOGGER.error(lu.print());
			
			jobResult = -1;
			throw new JobExecutionException(e.getCause());
		}
		// jobContext에 결과값을 저장한다.
		jobContext.setResult(jobResult);
    }
}
```



---

출처 : [동시사랑 블로그](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=ljpark6&logNo=221883162795)

