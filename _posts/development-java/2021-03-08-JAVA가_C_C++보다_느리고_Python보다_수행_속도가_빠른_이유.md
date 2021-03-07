---
layout: post
title:  "Java가 C, C++보다 느리고, Python보다 수행 속도가 빠른 이유"
subtitle: "Java가 C, C++보다 느리고, Python보다 수행 속도가 빠른 이유"
categories: development
tags: java
comments: true
---

**일괄 컴파일 방식 언어인 C, C++**의 코드는 컴파일만 하면 바로 CPU에서 실행이 가능한 코드인 기계어(native code)로 변환된다. 그래서 수행속도가 빠르다.  

**Java**의 코드는 컴파일을 해서 바이트 코드(byte code)를 생성하고, 그 바이트 코드(byte code)를 기계어(native code)로 변환하는 시간을 필요로 하기 때문에 수행 속도가 더 오래 걸린다. 그리고 Java는 인터프리터(interpreter)와 컴파일의 혼합 방식인 JIT 방식을 사용한다.

**Python과 같은 인터프리터 방식의 언어**는 코드를 한 줄 읽고 바로 결과를 출력한다. 그리고 같은 기능을 하는 코드가 다시 나와도 또 다시 해석하여 결과를 출력한다. 이 때문에 일반적으로 컴파일 방식의 언어보다 수행 속도가 느리다. 

위 언어의 수행 속도를 비교하면 일반적으로 `C, C++` > `Java` > `Python` 이다. (당연히 상황에 따라 예외는 존재한다.)

![image](https://user-images.githubusercontent.com/41244373/110245702-b6ea3380-7fa7-11eb-8f02-71aef4ba91b6.png)
