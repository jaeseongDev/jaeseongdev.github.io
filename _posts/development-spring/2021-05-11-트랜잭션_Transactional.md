---
layout: post
title:  "[트랜잭션] @Transaction 개념 / @Transactional을 활용해서 DB에 정보 반영하지 않기"
subtitle: "[트랜잭션] @Transaction 개념 / @Transactional을 활용해서 DB에 정보 반영하지 않기"
categories: development
tags: spring
comments: false
---

# [트랜잭션] @Transaction 개념 / @Transactional을 활용해서 DB에 정보 반영하지 않기

# @Transactional의 작동 원리

스프링은 @Transactional 애노테이션이 붙은 클래스에 프록시를 생성한다. 프록시는 트랜잭션 로직을 메서드 앞뒤에 넣어준다. 

# 특징

### 특징 1) @Transacational을 선언해주지 않으면, 예외가 발생해도 Commit이 되어버린다.

- **UncheckedException → Rollback (X)**
- **CheckedException → Rollback (X)**
    - **'@Transactional이 적용된 메서드'에서 CheckedExcpetion을 throws한 경우
    → Rollback (X)**
    - **'@Transactional이 적용된 메서드'에서 try-catch로 CheckedExcpetion을 처리한 경우
    → Rollback (X)**
    - **'@Transactional이 적용된 메서드' 자체에서 CheckedExcpetion을 받아서 UncheckedException을 던진 경우 → Rollback (X)**
- **Error → Rollback (X)**

### 특징 2) @Transactional을 선언하면 Default 설정으로,
UncheckedException, Error에 대해서 Rollback을 시킨다.
(CheckedException에 대해서는 Rollback을 시키지 않는다.)

- **@Transactional, UncheckedException → Rollback (O)**

    ```
    @Repository
    public class LineRepository {
    		**@Transactional**
    		public void createTwoLine() {
            lineDao.create("1호선", "yellow");
            lineDao.create("2호선", "red");
            throw new IllegalArgumentException("에러 발생");
        }
    }
    ```

    ```
    @SpringBootTest(webEnvironment = WebEnvironment.NONE)
    public class test {
        @Autowired
        private LineRepository lineRepository;
        @Autowired
        private LineDao lineDao;

        @Test
        public void test() {
            try {
                // 라인 2개 생성
                lineRepository.createTwoLine();
            } catch (Exception e) {
                System.out.println("에러 발생");
                // 생성된 라인 조회
                List<Line> lines = lineDao.findAll();
                for (Line line : lines) {
                    System.out.println(line.getName()); **// 아무것도 출력되지 않음**
                }
            }
        }
    }
    ```

    `createTwoLine()`에 `@Transcational` 처리가 되어 있어서, Line을 생성하고나서 예외가 발생했지만  rollback이 되었다. 즉, `createTwoLine()`을 실행시켜서 Line을 생성한 이후에 `createTwoLine()` 내부에서 예외가 발생했는데도 불구하고 Line이 생성되지 않았다. 

- **@Transactional, CheckedException → Rollback (X)**
    - **'@Transactional이 적용된 메서드'에서 CheckedExcpetion을 throws한 경우
    → Rollback (X)**

        ```
        @Repository
        public class LineRepository {
        		**@Transactional**
        		public void createTwoLine() **throw SQLException** {
                lineDao.create("1호선", "yellow");
                lineDao.create("2호선", "red");
                **throw new SQLException("에러 발생");**
            }
        }
        ```

        ```
        @SpringBootTest(webEnvironment = WebEnvironment.NONE)
        public class test {
            @Autowired
            private LineRepository lineRepository;
            @Autowired
            private LineDao lineDao;

            @Test
            public void test() {
                try {
                    // 라인 2개 생성
                    lineRepository.createTwoLine();
                } catch (**SQLException e**) {
                    System.out.println("에러 발생");
                    // 생성된 라인 조회
                    List<Line> lines = lineDao.findAll();
                    for (Line line : lines) {
                        System.out.println(line.getName()); **// '1호선', '2호선' 출력 됨**
                    }
                }
            }
        }
        ```

    - **'@Transactional이 적용된 메서드'에서 try-catch로 CheckedExcpetion을 처리한 경우
    → Rollback (X)**

        ```
        @Repository
        public class LineRepository {
        		**@Transactional**
        		public void createTwoLine() {
        				**try {**
                    lineDao.create("1호선", "yellow");
                    lineDao.create("2호선", "red");
                    **throw new SQLException("에러 발생");**
                **} catch (SQLException e) {
                    System.out.println("적절한 예외 처리");
                }**
            }
        }
        ```

        ```
        @SpringBootTest(webEnvironment = WebEnvironment.NONE)
        public class test {
            @Autowired
            private LineRepository lineRepository;
            @Autowired
            private LineDao lineDao;

            @Test
            public void test() {
                // 라인 2개 생성
                lineRepository.createTwoLine();
                System.out.println("에러 발생");
                // 생성된 라인 조회
                List<Line> lines = lineDao.findAll();
                for (Line line : lines) {
                    System.out.println(line.getName()); **// '1호선', '2호선' 출력 됨**
                }
            }
        }
        ```

    - **'@Transactional이 적용된 메서드'에서 try-catch로 CheckedExcpetion을 받아서 UncheckedException을 다시 던진 경우
    → Rollback (O)**

        ```
        @Repository
        public class LineRepository {
        		**@Transactional**
        		public void createTwoLine() {
        				**try {
                    lineDao.create("1호선", "yellow");
                    lineDao.create("2호선", "red");
                    throw new SQLException("CheckedException 발생");
                } catch (SQLException e) {
                    throw new IllegalArgumentException("UnCheckedException 발생");
                }**
            }
        }
        ```

        ```
        @SpringBootTest(webEnvironment = WebEnvironment.NONE)
        public class test {
            @Autowired
            private LineRepository lineRepository;
            @Autowired
            private LineDao lineDao;

            @Test
            public void test() {
                try {
                    // 라인 2개 생성
                    lineRepository.createTwoLine();
                } catch (Exception e) {
                    System.out.println("에러 발생");
                    // 생성된 라인 조회
                    List<Line> lines = lineDao.findAll();
                    for (Line line : lines) {
                        System.out.println(line.getName()); **// 아무것도 출력되지 않음**
                    }
                }
            }
        }
        ```

- **@Transactional, Error → Rollback (O)**

    ```
    @Repository
    public class LineRepository {
    		**@Transactional**
    		public void createTwoLine() {
    				**lineDao.create("1호선", "yellow");
            lineDao.create("2호선", "red");
            throw new OutOfMemoryError();**
        }
    }
    ```

    ```
    @SpringBootTest(webEnvironment = WebEnvironment.NONE)
    public class test {
        @Autowired
        private LineRepository lineRepository;
        @Autowired
        private LineDao lineDao;

        @Test
        public void test() {
            try {
                // 라인 2개 생성
                lineRepository.createTwoLine();
            } catch (OutOfMemoryError e) {
                System.out.println("에러 발생");
                // 생성된 라인 조회
                List<Line> lines = lineDao.findAll();
                for (Line line : lines) {
                    System.out.println(line.getName()); **// 아무것도 출력되지 않음**
                }
            }
        }
    }
    ```

**[스프링에 기본적으로 설정되어 있는 값]**

```java
**@Transactional**
public void saveBook() {
		...
}

// 위 코드와 동일
**@Transactional(rollbackFor = {RuntimeException.class, Error.class})**
public void saveBook() {
		...
}
```

### 특징 3) 테스트 코드에서 @Transactional을 사용하면, 각 테스트를 실행한 후 무조건 Rollback을 시킨다.

- 각 테스트에 독립적인 환경을 만들기 위해, `@BeforeEach`나 `@AfterEach`를 사용해서 DB를 초기화시켜줘야 할 때가 종종 있다. 하지만 스프링 부트에서는 이런 것들을 쉽게 할 수 있는 `@Transactional`이라는 어노테이션을 제공한다.
- 원래 `@Transactional`을 테스트 클래스에 입력해주면, 테스트 할 때에는 DB에 데이터를 다 넣어주고 테스트를 끝낸 뒤에는 Commit을 하지 않고 Rollback을 해버린다. 이로 인해 각각의 테스트를 실행시킬 때, DB에 실제 데이터를 반영하지 않기 때문에 독립적인 테스트가 가능해지는 것이다.
- 테스트 완료 후 자동으로 rollback 처리 한다.
(spring-boot-test는 단순히 spring-test를 확장한 것이기 때문에 @Test 어노테이션과 함께 @Transactional 어노테이션을 함께 사용하면 테스트가 끝날 때 rollback 처리)
- **예제 코드**

    ```
    @SpringBootTest(webEnvironment = WebEnvironment.**NONE**)
    **@Transactional**
    public class test {
        @Autowired
        private LineRepository lineRepository;
        @Autowired
        private LineDao lineDao;

        @Test
        public void test() {
            try {
                // 라인 2개 생성
                lineRepository.createTwoLine();
            } finally {
                // 생성된 라인 조회
                List<Line> lines = lineDao.findAll();
                for (Line line : lines) {
                    System.out.println(line.getName()); **// '1호선', '2호선' 출력 됨**
                }
            }
        }

        @Test
        public void test2() {
            try {
                // 라인 2개 생성
                lineRepository.createTwoLine2();
            } finally {
                // 생성된 라인 조회
                List<Line> lines = lineDao.findAll();
                for (Line line : lines) {
                    System.out.println(line.getName()); **// '3호선', '4호선' 출력 됨**
                }
            }
        }
    }
    ```

### 특징 4) `RANDOM_PORT`, `DEFINED_PORT`를 사용하면 실제 테스트 서버는 별도의 스레드에서 테스트를 수행하기 때문에 @Transactional을 사용해도 Rollback되지 않는다.

[스프링 부트 문서에 다음과 같이 나와있다.](https://docs.spring.io/spring-boot/docs/1.5.2.RELEASE/reference/html/boot-features-testing.html) 

> If your test is @Transactional, it will rollback the transaction at the end of each test method by default. However, as using this arrangement with either `RANDOM_PORT` or `DEFINED_PORT` implicitly provides a real servlet environment, HTTP client and server will run in separate threads, thus separate transactions. Any transaction initiated on the server won’t rollback in this case.

### 특징 5) **@Transactional 은 public 메소드에서만 정상 작동한다.**

정리 중....

### 특징 6) 트랜잭션이 적용되지 않은 public 메소드 내부에서 @Transactional이 적용된 public 메소드를 호출하는 경우, @Transactional이 동작하지 않는다.

정리 중...

# 주의

- **DB에 직접 접근하는 JdbcTemplate과 같은 코드에 대해서는 `@Transactional`이 정상적으로 적용된다. 하지만 RestAssured와 같이 외부에 API 요청을 하는 식은 `@Transactional`이 적용되지 않는다.**

# References

[[스프링부트 (9)] SpringBoot Test(2) - @SpringBootTest로 통합테스트 하기](https://goddaehee.tistory.com/211)

[스프링 트랜잭션](https://velog.io/@woo00oo/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98)

[[Spring] @Transactional 롤백은 언제 되는 걸까? - 예외가 발생했는데도 DB 반영이 된다고?](https://pjh3749.tistory.com/269)