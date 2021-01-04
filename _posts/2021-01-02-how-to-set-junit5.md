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

위의 링크에 들어가면 build.gradle에 아래의 빨간색으로 된 코드를 추가로 입력하면 Junit5를 사용할 수 있다고 되어있다.

**build.gradle**

{% include code-block.html content=
"plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

<span style='color:red !important'>test {
    useJUnitPlatform()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.6.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine'
}</span>"
%}

### 2. build.gradle을 수정했으니 다시 빌드하도록 밑의 버튼 클릭

![image](https://user-images.githubusercontent.com/41244373/103536326-a8af6680-4ed5-11eb-9573-594e6f21c066.png)

### 3. 테스트 코드 작성하기 (예시)

![image](https://user-images.githubusercontent.com/41244373/103536335-acdb8400-4ed5-11eb-9e4d-c700595568c3.png)

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

![image](https://user-images.githubusercontent.com/41244373/103536337-ad741a80-4ed5-11eb-81c0-f68b00df1cae.png)

### References
- **IntelliJ, JUnit5 사용하기! (feat. Gradle)** - [https://itbellstone.tistory.com/106](https://itbellstone.tistory.com/106)