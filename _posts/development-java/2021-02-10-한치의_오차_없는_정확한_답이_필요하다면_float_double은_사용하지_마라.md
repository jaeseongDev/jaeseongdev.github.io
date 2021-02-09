---
layout: post
title:  "한치의 오차 없는 정확한 답이 필요하다면 float, double은 사용하지 마라."
subtitle: "한치의 오차 없는 정확한 답이 필요하다면 float, double은 사용하지 마라."
categories: development
tags: java
comments: true
---

## float, double은 오차가 발생한다.

float와 double 타입은 이진 부동소수점 연산에 쓰이며, 넓은 범위의 수에 빠르게 정밀한 '근사치'로 계산하도록 세심하게 설계되었다. 따라서 정확한 결과가 필요할 때는 사용하면 안 도니다. float와 double 타입은 특히 정확한 값을 계산해야 되는 곳에서는 적절하지 않다. 

### 예시

```java
System.out.println(1.03 - 0.42); // 0.61000000000001 출력
```

## 정확한 답이 필요할 때에는 BigDecimal, int, long을 사용해라.

정확한 답이 필요한 계산에는 float나 double을 피하고, BigDecimal, int, long을 사용해라. 단, BigDecimal은 기본 타입보다 쓰기가 훨씬 불편하고, 훨씬 느리다. 코딩 시의 불편함이나 성능 저하를 신경 쓰지 않겠다면 BigDecimal을 사용해라.

- 성능이 중요하고 소수점을 직접 추적할 수 있고 숫자가 너무 크지 않다면 int나 long을 사용해라.
- 숫자를 아홉 자리 십진수로 표현할 수 있다면 int를 사용해라.
- 열여덟자리 십진수로 표현할 수 있다면 long을 사용해라.
- 열여덟 자리를 넘어가면 BigDecimal을 사용해라.

## 참고

- int, long → 정수만 표현 가능

- float, double, BigDecimal → 소수 표현 가능

## 소숫점을 가진 값은 어떻게 int, long을 사용하는가?

소숫점을 가진 값에 10의 n제곱을 곱해서 정수로 만든 뒤에 사용하면 된다. 예를 들면, 달러로 계산할 때 소숫점이 발생한다면 센트로 단위를 바꾸면 소숫점이 사라지면서 정수로만 계산을 할 수 있게 된다.

단, 소숫점을 직접 관리해야 하고, 다룰 수 있는 값의 크기가 제한된다는 단점이 있다. 

# References

- [Effective Java(이펙티브 자바) - 조슈아 블로크, 인사이트](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEa&Kc=) → [아이템 60. 정확한 답이 필요하다면 float와 double은 피하라.]