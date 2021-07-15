# Unit 5로 바뀌면서 나온 이슈들

1. ### Cast Issue

`.andExpect(jsonPath())` 를 사용하면서 강제로 ResultMatcher 형태로 형 변환을 해야하는 Issue가 발생하였다. 이 문제들은 기존에 사용하던 라이브러리가 바뀌면서 생긴 현상인데 import 구문을 바꿔주면 된다.

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.assertj.core.internal.bytebuddy.matcher.ElementMatchers.is;
import static org.mockito.Mockito.doReturn;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
      
```

를

```java
import static org.hamcrest.core.Is.is;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
```

와 같은 형태들로 바꿔줘야 정상적으로 작동하게 된다. 특히 형 변환을 해준다고 해도 실제 Test를 해보면 테스트 결과가 틀리게 나오는 것을 알 수 있었다.

2. ### @Test

`@Test` 의 import를 바꿔 줘야 한다.

> `org.junit.Test`
>
> 를
>
> `org.junit.jupiter.api.Test`
>
> 로 바꿔야한다.



3. ### @RunWith

해당 어노테이션의 경우 Junit5가 되면서 `@ExtendWith`로 변경되었습니다. 

> `org.junit.runner.RunWith` 
>
> 를
>
> `org.junit.jupiter.api.extension.ExtendWith`
>
> 로 바꿔야 한다.

그리고 기존에 `@RunWith` 어노테이션을 모두다 `@ExtendWith`로 바꿔야한다.

4. ### SpringRunner

`SpringRunner`역시 `SpringExtension`로 변경되었기 때문에 클래스명과 패키지 위치 2개다 변경이 필요합니다.

> `org.springframework.test.context.junit4.SpringRunner`
>
> 를
>
> `org.springframework.test.context.junit.jupiter.SpringExtension`
>
> 로 바꿔야 한다.

그리고 기존에 `SpringRunner`로 선언한 클래스를 `SpringExtesion`으로 바꿔야 한다.

5. ### @After

마찬가지로 `@After`가 역시 `@AfterEach` 로 변경되었기 때문에 어노테이션과 패키지 위치 2개다 변경이 필요합니다.

>```
>org.junit.After
>```
>
>를
>
>```
>org.junit.jupiter.api.AfterEach
>```
>
>로 바꿔야한다.

그리고 기존에 `@After`어노테이션을 `@AfterEach`로 바꿔야한다.

6. ### @Before

After와 마찬가지로 `@Before`가 역시 `@BeforeEach` 로 변경되었기 때문에 어노테이션과 패키지 위치 2개다 변경이 필요합니다.

> ```
> org.junit.Before
> ```
>
> 를
>
> ```
> org.junit.jupiter.api.BeforeEach
> ```
>
> 로 바꿔야 한다

그리고 기존에 `@Before`어노테이션을 `@BeforeEach`로 바꿔야한다.

7. ### 중요!!

위의 내용대로 바꿔서 테스트를 진행하면 아래와 같은 에러가 발생한다.

```
No tests found for given includes
```

이는 `build.gradle`에 옵션을 추가하지 않아서 발생한다. 아래와 같은 코드를 `build.gradle`에 추가해주자.

```
test {
    useJUnitPlatform()
}
```



---

참고.

[jsonPath cast issue in Junit5 spring boot with MockMVC](https://stackoverflow.com/questions/63649712/jsonpath-cast-issue-in-junit5-spring-boot-with-mockmvc)

[2020.12.16스프링부트와 AWS로 혼자 구현하는 웹 서비스 최신 코드로 변경하기](https://jojoldu.tistory.com/539#recentComments)

