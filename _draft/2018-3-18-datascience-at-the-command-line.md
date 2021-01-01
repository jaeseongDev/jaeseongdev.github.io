---
layout: post
title:  "데이터 사이언스에서 Command Line 사용하기"
subtitle:   "데이터 사이언스에서 Command Line 사용하기"
categories: development
tags: linux
comments: true
---
데이터 사이언스에서 커맨드 라인(쉘 스크립트)을 사용하는 것에 대해 작성한 글입니다.

해당 책을 참고했습니다(링크 첨부하기)

앞으로 아래와 같은 커맨드 라인을 자주 접하게 될 것입니다

```
$ curl -s http://www.gutenberg.org/files/98/98-0.txt | \
grep -oE '\w+' | \
sort | \
uniq -c | \
sort -nr | \
head -n 5
```

1. ```curl``` 명령어를 통해 해당 데이터 다운로드
2. ```grep``` 명령어로 정규표현식 패턴을 넣어 단어를 행별로 추출해 정리
3. ```sort``` 명령어로 오름차순 정리
4. ```unique``` 명령어로 중복을 제거하고 ```-c``` 옵션을 줘서 중복수를 count
5. ```sort``` 명령어로 단어 갯수를 내림차순으로 정리
6. ```head``` 명령어로 가장 빈도가 높은 단어 5개를 추출합니다


```
$ echo 'foo\nbar\nfoo' | sort | uniq -c | sort -nr | awk '{print $2", "$1}'
>>> foo, 2
>>> bar, 1
```

https://statkclee.github.io/ml/00-toolchain-cmd.html