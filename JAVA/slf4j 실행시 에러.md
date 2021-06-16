# log4j Java 실행시 에러

```
log4j:WARN No appenders could be found for logger (org.quartz.impl.StdSchedulerFactory).
log4j:WARN Please initialize the log4j system properly.
log4j:WARN See http://logging.apache.org/log4j/1.2/faq.html#noconfig for more info.
```

slf4j 로그를 쓰고싶었는데 실행하니 위와 같은 에러가 떴다.

여러 블로그를 참조해 보고 찾아봤지만 나는 실행환경이 Simple Java Project여서 의존성 추가나 slf4j 설정을 만져주는 등 나에게는 올바르지 않았다.

그러던 중 간단한 해결 방법을 찾았다.

역시 [스택오버플로우](https://stackoverflow.com/questions/12532339/no-appenders-could-be-found-for-loggerlog4j)...

방법은 main()의 첫번째 줄에 아래의 코드를 넣어서 실행하는 것이다.

```java
BasicConfigurator.configure();
```

그렇고 다시 시작하니 잘 되었다...

왜 안됬던 건지는 모르겠지만 흠.... 되니 일단 안심 ㅎㅎ
