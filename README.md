### Feature
- [구글 애널리틱스](https://analytics.google.com/analytics/web/?hl=ko&pli=1) (유저 데이터 분석용)
- [댓글 기능](https://blog.disqus.com/)
- [구글 검색 엔진 트래킹](https://search.google.com/search-console/about)
- [네이버 검색 엔진 트래킹](https://searchadvisor.naver.com/)

### Structure
- 다른 분들이 이 테마를 Fork할 경우, 사용할 수 있도록 블로그 구조에 대해 설명합니다

```
├── README.md
├── _config.yml : 기본 설정이 저장된 파일
├── _data : 유저 데이터가 저장된 폴더, author.yml만 수정하면 됨
├── _draft : 초안 작성 폴더, 커밋해도 반영되지 않음
├── _featured_categories : 카테고리(메뉴판의 큰 제목)
├── _featured_tags : 카테고리의 태그(메뉴판의 소제목)
├── _includes : 기본 홈페이지 포맷
├── _ipynbs : ipynb 저장 폴더
├── _js : 자바스크립트 소스 저장 폴더
├── _layouts : 타입별 레이아웃 폴더
├── _plugins : 플러그인 저장 폴더. 그러나 Github에서 빌드시 플러그인 사용 불가능
├── _posts : 글 저장 폴더
├── _sass
├── _site : 빌드시 생기는 폴더, 신경쓸 필요 없음
├── about.md : about에서 나타날 내용
├── assets : css, js, img 등 저장하는 폴더
├── favicon.ico : favicon 아이콘
├── feed.xml
├── index.html
├── robots.xml
├── search.html
├── sitemap.xml
├── tile-wide.png
└── tile.png
```

- ```_config.yml```, ```_data```, ```_featured_categories```, ```_featured_tags```, ```about.md``` 내용 수정
- ```favicon.ico```, ```tile-wide.png```, ```tile.png``` 원하는 이미지로 설정

### 로컬 빌드
- Ruby가 설치되어 있어야 합니다
- Ruby 설치는 [공식 문서](https://www.ruby-lang.org/ko/documentation/installation/) 참고

```
bundle exec jekyll serve
```

### 원격 빌드
- Github 저장소에 Push

### 글 작성
- ```_featured_categories```, ```_featured_tags``` 설정한 후, ```_posts```에 글을 작성합니다
- 글 제목 형태는 ```2018-01-03-title1.md``` 이런 방식처럼 작성! 날짜를 빼고 쓰면 반영되지 않습니다
- 코드에 빨간 색으로 Highlihgting을 하고 싶은 경우, md 파일 내에 밑과 같이 코드를 작성하면 된다. 
```
---
layout: post
title:  "첫 글"
subtitle: "첫 글"
categories: etc
tags: diary
comments: true
---

(일반적인 마크다운 코드 작성)

{% include code-block.html content=
"<span style='color:red !important'>빨간색을 적용하고 싶은 코드의 줄</span>
일반 코드를 작성하고 싶은 코드의 줄
일반 코드를 작성하고 싶은 코드의 줄"
%}

(일반적인 마크다운 코드 작성)
```

### Commit 메시지 형태
**기능 관련**
- ```feat : ...``` : 새로운 기능 추가
- ```fix : ...``` : 기능, 버그 수정
- ```docs : ...``` : Readme 수정

**글, 문구, 메뉴 관련**
- ```post : ...``` : 새로운 글 작성
- ```modify : ...``` : 글, 문구, 메뉴 수정

### Github Blog
- 이 블로그는 [변성윤](https://github.com/zzsza/zzsza.github.io), [박민](https://github.com/isme2n/isme2n.github.io)님 블로그 테마를 기반으로 제작되었습니다
- 본 테마를 사용하고 싶으신 경우, issue 또는 메일([qkrwotjd1445@naver.com](qkrwotjd1445@naver.com))로 사용 요청을 해주세요:)

---
### 추가할 기능
- disqus의 비정상 작동으로 인해, Facebook 댓글 기능으로 변경
- Google 애드센스 달기