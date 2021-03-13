---
layout: post
title:  "꼭 변경해야 할 필드를 뺀 나머지는 모두 private final로 선언해라."
subtitle: "꼭 변경해야 할 필드를 뺀 나머지는 모두 private final로 선언해라."
categories: development
tags: java
comments: false
---

필드(클래스 내의 변수)는 변경할 수 있는 부분을 최소한으로 줄이자. 객체가 가질 수 있는 상태의 수를 줄이면 그 객체를 예측하기 쉬워지고 오류가 생길 가능성이 줄어든다. 그러므로 **꼭 변경해야 할 필드를 뺀 나머지 모두를 private final로 선언하자.**

# References

- [Effective Java(이펙티브 자바) - 조슈아 블로크, 인사이트](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEa&Kc=) → [아이템 17. 변경 가능성을 최소화하라.]