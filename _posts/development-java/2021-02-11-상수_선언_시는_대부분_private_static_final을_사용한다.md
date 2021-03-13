---
layout: post
title:  "상수 선언 시는 대부분 private static final을 사용한다. (+ static을 같이 붙이는 이유)"
subtitle: "상수 선언 시는 대부분 private static final을 사용한다. (+ static을 같이 붙이는 이유)"
categories: development
tags: java
comments: false
---

# 상수 선언 시 private static final 사용

### 예시 코드

```
public class Pins {
		**private static final** int MIN_PINS = 0;
		private static final int MAX_PINS = 10;
		...
}
```

- 상수는 final로만 선언하지 말고, 반드시 static final로 선언해라.
- 값을 하드 코딩하지 마라 - 상수(static final)을 만들어라.

# '상수'인 final에 static을 관례적으로 붙이는 이유

런타임 중에 데이터가 할당 및 헤제 되는 동적 데이터와는 달리, static 데이터는 프로그램 실행 직후부터 끝날 때까지 메모리 수명이 유지된다. static을 사용한다는 의미는 해당 데이터를 어디서든 공유 하겠다는 의미이다. 여기저기서 해당 객체를 사용한다면, 그 객체는 오직 하나의 데이터를 사용하는 것이다. 그래서 static이 선언된 변수 값을 바꿔버리면 다른 곳에서 해당 변수의 값을 참조하는 부분의 값도 같이 변하게 된다.  

static을 사용하게 되면 해당 클래스에서 사용할 데이터를 프로그램을 시작하는 순간 바로 메모리에 올려 고정시키게 된다. 단, 이 데이터들은 모든 클래스 인스턴스에서 똑같이 써야할 값이고, 프로그래머는 이들을 프로그램 처음부터 끝까지 바뀌지 않는 논리일 때만 쓸 것입니다. 

상수는 모든 인스턴스에서 똑같이 써야되는 값이므로, 인스턴스가 만들어질 때마다 새로 메모리 공간을 생성하게 되면 비효율적일 것이다. 공간적인 메모리 비효율과, 시간적인 메모리 비효율 2가지가 발생한다. 이러한 이유 때문에 상수에는 static을 붙여서 사용하는 것이다. 

# References

- [getter 메소드를 사용하지 않도록 리팩토링한다.](https://www.slipp.net/questions/565)

- [왜 자바에서 final 멤버 변수는 관례적으로 static을 붙일까?](https://djkeh.github.io/articles/Why-should-final-member-variables-be-conventionally-static-in-Java-kor/)

- [[Java] final static](https://wonyong-jang.github.io/java/2020/03/28/Java-Static-Final.html)