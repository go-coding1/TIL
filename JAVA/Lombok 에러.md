# Gradle Lombok 어노테이션 적용 안되는 문제 해결법

스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 따라하던 도중 Lombok 관련된 설정에서 에러가 발생하였다.

내가 사용하던 개발환경은 이렇다.

>Gradle : gradle-7.0-bin.zip
>
>JAVA :  16

위 와 같은 상태에서 Gradle.build 파일에 아래와 같이 적어주었다.

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.projectlombok:lombok'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

그 후 `@Getter` 와 `@RequireContructor` 에 대한 테스트 코드를 실행해 보았는데 두 어노테이션이 작동하지 않았다. 

난 분명히 Lombok 라이브러리를 추가해 줬고 Compile 설정에서도 Enable Annotation Processing 체크도 해주었다. 

이것 저것 시도해 보면서 검색한 결과 몇가지 설정을 더 해줘야 했다.

1. 첫번째는 `annotationProcessor 'org.projectlombok:lombok' ` 구문을 gradle.build에 추가해 줘야한다는 것이고
2. 두번째는 Lombok에 대한 버전을 명시해줘야 한다는 것이다. 특히 **자바 16 부터는 롬복 1.18.20 버전과의 호환성이 지원된다. 이전 버전은 호환성이 맞지 않아 제대로 작동하지 않는다고 한다.**

위의 2가지를 토대로 gradle.build 파일을 다시 작성해 주었다.

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.projectlombok:lombok:1.18.20'
    annotationProcessor 'org.projectlombok:lombok:1.18.20'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

이제 정상적으로 작동하기 시작했다.

---

참고 링크들

[Annotation Processing 설정](https://gracelove91.tistory.com/13)

[Gradle과 Java버전에 따른 호환성 문제](https://velog.io/@roo333/%EC%97%90%EB%9F%AC-Lombok)

