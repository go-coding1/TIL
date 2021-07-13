# 낮은 버전의 Gradle에서의 compile 과  implementation

스프링 부트와 AWS로 혼자 구현하는 웹 서비스를 따라하던 중 발생한 일이였다.

처음 `build.gradle` 을 설정하는 과정에서 계속해서 알 수 없는 오류가 나타났었다. 내 코드는

```gradle
buildscript {
    ext{
        springBootVersion = '2.1.7.RELEASE'
    }

    repositories {
        mavenCentral()
        jcenter()
    }

    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")

    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'


group 'com.jojoldu.book'
version '1.0-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile('org.springframework.boot:spring-boot-starter-web')
    testCompile('org.springframework.boot:spring-boot-starter-test')
}
```

이였고 에러의 내용은 

![image](https://user-images.githubusercontent.com/54675591/125455404-baa3f8d5-e7e9-4876-81cd-efe80895fe63.png)

위와 같았다. 

주요 내용은 **Could not find method compile() for arugments [org.springframework.boot:spring-boot-starter-web]** 이라는 내용이였다.

처음에는 이해할 수 가 없었다. 아니 나는 dependencies에 compile()구문을 제대로 써주었는데 알 수가 없다니 흠...

3시간의 구글링을 한 결과 책 저자가 쓴 글을 발견하였다.

[링크](https://jojoldu.tistory.com/539#recentComments)

내용을 정리하자면 높은 버전의 Gradle에서는 compile()을 사용하는게 아니라 implementation 구문을 사용해줘야 한다였다.

나는 Gradle 버전이 7버전이였기 때문에 compile이 작동하지 않았던 것이다. 

종종 위 링크를 참조하면서 책을 읽어야 겠다.
