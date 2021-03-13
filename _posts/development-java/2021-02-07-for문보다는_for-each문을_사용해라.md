---
layout: post
title:  "for문보다는 for-each문을 사용해라."
subtitle: "for문보다는 for-each문을 사용해라."
categories: development
tags: java
comments: false
---

# for문의 단점

```java
for (Iterator<Element> i = c.iterator(); i.hasNext();) {
    Element e = i.next();
    ... // e로 무언가를 한다. 
}
```

```java
for (int i = 0; i < a.length; i++) {
    ... // a[i]로 무언가를 한다.
}
```

- 반복자(`Iterator`)와 인덱스(`int i`) 변수는 코드를 지저분하게 한다. 이러한 변수들이 늘어나면 오류가 생길 가능성이 높아진다. 왜냐하면 변수를 잘못 사용할 틈새가 넓어지는 것이기 때문이다. 혹시라도 잘못된 변수를 사용했을 때 컴파일러가 잡아주리라는 보장도 없다.
- 컬렉션이나 배열이냐에 따라 코드 형태가 달라지므로 에러 발생 가능성이 높다.

# for문의 단점을 극복한 for-each문(향상된 for문)

```java
for (Element e : elements) {
    ... // e로 무언가를 한다.
}
```

- 반복자(`Iterator`)와 인덱스(`int i`) 변수를 사용하지 않으니 코드가 깔끔해지고 오류가 날 일도 없다.
- 동일한 코드 형태로 컬렉션과 배열을 모두 처리할 수 있어서 에러 발생 가능성이 낮다.
- for문에 비해서 성능 저하도 없다.

# for-each문을 사용할 수 없는 상황

### 리스트, 배열 원소 값을 수정하는 경우

리스트나 배열을 순회하면서 그 원소의 값 일부 혹은 전체를 교체해야 한다면 리스트의 반복자(`Iterator`)나 배열의 인덱스(`int i`)를 사용해야 한다.

# 결론

**for-each문을 사용할 수 없는 상황이 아니라면 for문 대신에 for-each문을 사용해라.**

# References

- [Effective Java(이펙티브 자바) - 조슈아 블로크, 인사이트](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEa&Kc=) → [아이템 58. 전통적인 for문보다는 for-each문을 사용하라.]