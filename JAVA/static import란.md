# import static 이란?

Quartz 스케줄링 코딩을 하던 중 특정함수를 써야하는데 import가 계속해서 되지 않는 상황이 발생했다. 이것 저것 시도해보다 github에 들어가보니 `import static`이란 걸 사용해서 코딩했다는 사실을 알게 되었다. JAVA를 시작한지 2년이 되어가는데 처음보는 코드였다. ㅜㅜ 아직 부족한가 보다. `import static`에 대해서 정리해보자!!



정확하게 부르는 명칭음 `static import` 이다. `static import`문은 JDK1.5부터 추가된 기능이다. 

import문을 사용하면 클래스의 패키지명을 생략할 수 있는 것과 같이 `static import`문을 사용하면 static멤버를 호출할 때 클래스명을 생략할 수 있다. 코드가 간결해지고 특정 클래스의 static 멤버를 자주 사용할 때 편리하지만 남용하면 독이 될 수 있기 때문에 주의해야 한다.

```java
import static java.lang.Integer.*;	//Integer 클래스의 모든 static 멤버
import static java.lang.Math.random;	//Math.random()만 import. 괄호는 붙이지 않는다.
import static java.lang.System.out;		//이렇게 하면 System.out 을 out만으로 참조 가능
```

위와 같이 static import 문을 선언하면

```java
//static import 문 선언 전
System.out.println(Math.random());
```

을

```java
//staic import 문 선언 후
out.println(random());
```

위 코드를 아래 코드와 같이 간략히 할 수 있다.

뭐 딱히 변한건 없다. 크게 대단할건 없었다.

---

아래는 Quartz 자바코드를 사용할때 `import`문과 `static import`문을 사용한 2가지 코드를 보여주겠다. 별 차이 없다는 다른 예제이다.

import 문 사용

```java
import org.quartz.JobBuilder;
import org.quartz.JobDetail;
import org.quartz.SchedulerException;
import org.quartz.Trigger;
import org.quartz.TriggerBuilder;


public class QuartzTest {

	public static void main(String[] args) throws SchedulerException, InterruptedException {
		// TODO Auto-generated method stub
		
		//Job 구현 내용이 담긴 HelloJob으로 JobDetail 새엇ㅇ
		JobDetail jobDetail = JobBuilder.newJob(HelloJob.class)
							.build();
		
		//실행 시점을 결정하는 Trigger 생성
		Trigger trigger = TriggerBuilder.newTrigger().build();
	}

}
```

static import 문 사용

```java
import org.quartz.JobDetail;
import org.quartz.SchedulerException;
import org.quartz.Trigger;

import static org.quartz.JobBuilder.newJob;
import static org.quartz.TriggerBuilder.newTrigger;

public class QuartzTest {

	public static void main(String[] args) throws SchedulerException, InterruptedException {
		// TODO Auto-generated method stub
		
		//Job 구현 내용이 담긴 HelloJob으로 JobDetail 새엇ㅇ
		JobDetail jobDetail = newJob(HelloJob.class)
							.build();
		
		//실행 시점을 결정하는 Trigger 생성
		Trigger trigger = newTrigger().build();
	}

}
```

