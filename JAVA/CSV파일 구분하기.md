# JAVA CSV 파일 ""안의 , 무시하기

[링크](https://hashcode.co.kr/questions/948/%EB%AC%B8%EC%9E%90%EC%97%B4%EC%9D%84-%EC%BD%A4%EB%A7%88%EB%A5%BC-%EA%B8%B0%EC%A4%80%EC%9C%BC%EB%A1%9C-%EB%B6%84%EB%A6%AC%ED%95%98%EB%8A%94%EB%8D%B0-%EC%95%88%EC%97%90-%EC%9E%88%EB%8A%94-%EC%BD%A4%EB%A7%88%EB%8A%94-%EB%AC%B4%EC%8B%9C%ED%95%98%EA%B2%8C-%ED%95%A0%EC%88%98%EC%97%86%EB%82%98%EC%9A%94)

[참고](https://github.com/dream-ellie/regex)

일하던중 CSV 읽는 형식을 짜야하는 순간이 왔다. 개발환경이 JDK1.6 이여서 openCSV라이브러리가 작동하지 않는다 ㅜㅜ

단순히 한줄씩 읽어 `split(",")`으로 잘라주고 싶었지만 예외의 상황이 발생했다.

우리가 알고있던 csv파일 형식은

```text
a,b,c,d,f
value1,value2,value3,value4,value5
value11,value22,value33,value44,value55
```

형식일 것이다. 하지만 만약에 값이 문자열이라면? 그 문자열 안에 `,`가 들어가 있는게 정상이라면? `split(",")`으로는 문제가 발생할 것이다.

```text
a,b,c,d,f
value1,"Value2 have , char value",value3,value4,value5
value11,value22,value33,value44,value55
```

`""`로 문자열을 감싸는 경우가 생긴다. 심지어 그 안에 `,` 가 들어가면 큰일이다.



정답은 문자열 정규화를 사용했다. 

`split(",")`으로 나눴던 문자열을

```java
split(",(?=([^\"]*\"[^\"]*\")*[^\"]*$)",-1);
```

을 사용하면 된다!!!



와 openCSV를 사용할 필요가 없겠는걸? 근데 이렇게 짤수 있으면 openCSV 안쓰는게 이득 아닌가?

나중에 정규식 제대로 각 잡고 공부해야 겠다는 생각이 들었다 ㅜㅜ

