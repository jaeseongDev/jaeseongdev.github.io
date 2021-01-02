---
layout: post
title:  "Gradle에서 Junit5 셋팅하는 방법"
subtitle: "Gradle에서 Junit5 셋팅하는 방법"
categories: development
tags: java
comments: false
---
### 0. 사전 셋팅

인텔리제이를 활용해서 gradle로 빌드할 기본 프로젝트를 생성

### 1. build.gradle에 들어가서 Junit5를 사용하기 위한 코드 추가

[JUnit5 공식 Docs](https://docs.gradle.org/current/userguide/java_testing.html#using_junit5)

위의 링크에 들어가면 build.gradle에 아래의 코드를 입력하면 Junit5를 사용할 수 있다고 되어있다.

**추가할 코드**

```
test {
    useJUnitPlatform()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'
}
```

**build.gradle**

```
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

test {
    useJUnitPlatform()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'
}
```

### 2. build.gradle을 수정했으니 다시 빌드하도록 밑의 버튼 클릭

![image](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2ce8f05f-305a-46ad-8629-6ad82b1add4f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210102%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210102T081331Z&X-Amz-Expires=86400&X-Amz-Signature=431e3792c067ccd4f2f15cf7846b67f270d640621264c46c49573cd88f914daf&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

### 3. 테스트 코드 작성하기 (예시)

![image](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/70ad0434-f9f4-43f0-b39d-9a54900c460b/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210102%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210102T082039Z&X-Amz-Expires=86400&X-Amz-Signature=d6a233cf0085b912befd78f9baa8f70ac0d44e84466e6541543bccd6f7e22414&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

**main/java/Dollar.java**

```java
public class Dollar {
    int amount;

    Dollar(int amount) {

    }

    void times(int multiplier) {

    }
}
```

**test/java/AppTest**

```java
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

public class AppTest {
    @Test
    public void testMultiplication() {
        Dollar five = new Dollar(5);
        five.times(2);
        assertEquals(10, five.amount);
    }
}
```

### 4. 테스트 실행해보기

![image](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/0fe21318-dbe0-48f8-9895-e4056dbcea9f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210102%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210102T082111Z&X-Amz-Expires=86400&X-Amz-Signature=89eb4a33aaab13e01c1c52b8f62e90151af366e907123e0a54b979e47ecc8d5b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22)

### 참고
[IntelliJ, JUnit5 사용하기! (feat. Gradle)](https://itbellstone.tistory.com/106)