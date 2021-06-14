---
layout: post
title:  "트랜잭션 격리 수준(Transaction Isolation Level)"
subtitle: "트랜잭션 격리 수준(Transaction Isolation Level)"
categories: development
tags: database
comments: false
---

# 트랜잭션 격리 수준(Transaction Isolation Level)

# 트랜잭션 격리 수준

데이터베이스에서 제공하는 격리성 수준(Transaction Isolation Level)에 대해서 알아보자. 아래 4개의 격리수준은 ANNSI/ISO SQL 표준(SQL92)에서 정의한 내용이다. 

# 트랜잭션 격리성(Transaction Isolation)

ACID 특징 중 격리성(Isolation)에 해당하는 부분을 조금 더 깊게 살펴보자. 격리성은 '실행 중인 트랜잭션의 중간 결과를 다른 트랜잭션이 접근할 수 없다.'라는 정의를 가지고 있다. 막연하게 접근할 수 없다라기보다는 일반적으로 접근 레벨이 있으며, DB에 따라 설정이 가능하다. 이런 격리성은 강하게 처리할 수 있으며 반대로 약하게 처리할 수도 있다. 이러한 격리성의 정도를 '트랜잭션 격리 수준(Transaction Isolation Level)'이라고 한다. 

> DB에서는 격리 수준(Isolation Level)을 설정함으로써 어떤 범위까지 Lock을 자동으로 걸도록 만들 지 결정할 수 있는 것이다.

DB 엔진(ex. InnoDB)은 격리 수준(isolation level)에 따라 서로 다른 locking 전략을 취할 수 있다. 격리 수준이 높을 수록 더 많이, 더 빡빡하게 lock을 건다. 따라서 각각의 격리 수준을 언제 사용해야 하는지, 혹은 각 격리 수준의 위험성은 무엇인지 알기 위해서는 각 격리수준별 locking 전략을 파악해야 한다. 

# 용어 정리

### Consistent Read

**Consistent Read란 read(=`SELECT`) operation을 수행할 때, 현재 DB의 값이 아닌 특정 시점의 DB snapshot을 읽어오는 것**이다. 물론 **이 snapshot은 commit 된 변화만이 적용된 상태를 의미**한다. 

InnoDB엔진은 consistent read를 하기 위해 Lock을 사용하지 않는다. 왜냐하면 동시성이 너무 떨어지기 때문이다. 즉, 동시에 DB를 접근하지 못하게 되므로 성능이 떨어진다는 것이다. 이 떄문에 InnoDB 엔진은 실행했던 쿼리의 log를 통해 consistent read를 지원한다. InnoDB 엔진은 각 쿼리를 실행할 때마다 실행한 쿼리의 log를 차곡차곡 저장한다. 그리고 나중에 consistent read를  할 때 이 log를 통해 특정 시점의 DB snapshot을 복구해서 가져온다. 이 방식은 비록 복구하는 비용이 발생하긴 하지만, lock을 활용하는 방식보다 높은 동시성을 얻을 수 있다. 

# 트랜잭션 격리 수준

## 1. Read Uncommitted

**트랜잭션에서 처리 중인 아직 커밋 되지 않은 데이터를, 다른 트랜잭션이 읽는 것을 허용한다.** 해당 수준에서는 **Dirty Read**, **Non-Repeatable Read**, **Phantom Read**가 일어날 수 있다. 이 설정은 정합성(데이터들이 서로 모순없이 일치해야 한다는 뜻)에 문제가 있기 때문에 권장하는 설정이 아니다. 

Read Uncommitted 방식이 가능한 이유는 InnoDB 엔진이 트랜잭션을 commit 하는 방법 때문이다. InnoDB 엔진은 일단 실행된 모든 쿼리를 DB에 적용한다. 그것이 아직 commit 되지 않았어도 적용한다. 즉, 특별히 log를 보고 특정 시점의 snapshot을 복구하는 consistent read를 하지 않고 그냥 해당 시점의 DB를 읽으면 dirty read가 된다. 

### 특징

- COMMIT 되지 않은 데이터라도 다른 트랜잭션에서 접근할 수 있다. 
(=INSERT, UPDATE, DELETE 후에 COMMIT이나 ROLLBACK에 상관없이 현재의 데이터를 읽어온다.)
- ROLLBACK이 될 데이터도 읽어올 수 있으므로 주의가 필요하다.
- 아무런 LOCK이 발생하지 않는다.

## 2. Read Committed

**Read Committed는 트랜잭션이 commit되어서 확정된 데이터만, 다른 트랜잭션이 읽을 수 있도록 허용함으로써 isolation을 보장**하는 level이다. 

Repetable Read 트랜잭션이 첫 read operation을 기준으로 consistent read를 수행하는 반면, **Read Committed 트랜잭션은 read operation마다 DB snapshot을 다시 뜬다.** 그렇기 때문에 다른 트랜잭션이 commit 한 다음에 다시 read operation을 수행하면, Repeatable Read와는 다르게 Read Committed 트랜잭션은 변화된 데이터를 조회할 수 있게 된다. 

"엥? Commit된 데이터만 보는 건 당연한 거 아니야?" 혹은 "SELECT 쿼리마다 snapshot을 다시 뜨면 다음 read에서 복구할 필요가 없는데 snapshot을 왜 뜨는거야?"라는 생각이 들 수도 있다. 실제 DB에는 아직 commit 되지 않은 쿼리도 적용된 상태이다. 즉, snapshot을 뜨지 않고 SELECT를 해버리면 commit 되지 않은 데이터에 대해서도 조회가 되버리는 것이다. 따라서 commit된 데이터만을 읽어오기 위해서는, 아직 commit은 되지 않았지만 DB에 들어가있는 데이터를, 아직 데이터가 들어가있지 않은 상태로 복구하는 과정이 필요한 것이다. 즉, consistent read를 수행해야 한다. 

'일반적인 non-locking `SELECT`' 외에 'lock을 사용하는 SELECT(`SELECT ... FOR SHARING`, `SELECT ... FOR UPDATE`)나 `UPDATE`, `DELETE` 쿼리를 실행할 때, Read Committed 트랜잭션은 Record Lock만 사용하고, Gap Lock은 사용하지 않는다. 

따라서 **Dirty Read**의 발생 가능성을 막는다. 커밋 되지 않은 데이터에 대해서는 실제 DB 데이터가 아닌 Undo 영역에 백업되어 있는 레코드를 가져온다. 하지만 **Non-Repeatable Read**와 **Phantom Read**에 대해서는 발생 가능성이 있다.

### 특징

- COMMIT된 데이터에 대해서만 다른 트랜잭션이 접근할 수 있다.
- 아무런 LOCK이 발생하지 않는다.

## 3. Repetable Read (MySQL Default 값)

**Repeatable Read는 InnoDB 스토리지 엔진에서 기본적으로 사용되는 격리 수준**이다. **Repeatable Read는 반복해서 Read(=`SELECT`) Operation을 수행하더라도, 읽어 들이는 값이 변화하지 않는 정도의 isolation을 보장하는 level**이다. 이 때문에 Non-repetable Read가 발생하지 않는다.

Repeatable Read 트랜잭션은 처음으로 read(`SELECT`) operation을 수행한 시간을 기록한다. 그리고 그 이후에는 모든 read operation마다 해당 시점을 기준으로 consistent read를 수행한다. 그러므로 트랜잭션 도중에 다른 트랜잭션이 commit 되더라도 새로 commit된 데이터는 보이지 않는다. 첫 read 시의 snapshot을 보기 때문이다. 

'일반적인 non-locking `SELECT`' 외에 'lock을 사용하는 SELECT(`SELECT ... FOR SHARING`, `SELECT ... FOR UPDATE`)나 `UPDATE`, `DELETE` 쿼리를 실행할 때, Repeatable Read 트랜잭션은 Gap Lock을 활용한다. 즉, 내가 조작을 가하려고 하는 Row의 후보군을 다른 트랜잭션이 건들지 못하도록 한다.

따라서 삭제와 수정에 대해서 트랜잭션 내에서 불일치를 가져오던 **Non-Reapeatable Read를 해소할 수 있다.** 그러나 **표준 규약에 따른 Repeatable Read에서는 Phantom Read라는 부정합은 여전히 발생**한다. 하지만 예외적으로 InnoDB에서는 Consistent Read를 활용하기 때문에 Phantom Read라는 부정합은 발생하지 않는다. (참고 : [https://www.tutorialfor.com/blog-264629.htm](https://www.tutorialfor.com/blog-264629.htm))

- **참고) Repetable Read vs Read Committed**

    Phantom Read가 일어나는 상황을 자세히 알아보자. c1 column이 있는 table t가 있다. 현재 t에는 `t.c1 = 13`인 row와 `t.c1 = 17`인 row가 존재한다. 여기서 Read Committed 트랜잭션 A와 트랜잭션 B가 아래와 같이 쿼리를 실행하려고 한다. 

    ```sql
    (Transaction A - READ COMMITTED)
    (A-1) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
    (A-2) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
    (A-3) COMMIT;
    ```

    ```sql
    (Transaction B - READ COMMITTED)
    (B-1) INSERT INTO t VALUES(15);
    (B-2) COMMIT;
    ```

    두 트랜잭션이 다음과 같은 순서로 실행되었다고 해보자. 

    ```sql
    (A-1) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
    (B-1) INSERT INTO t VALUES(15);
    (B-2) COMMIT;
    (A-2) SELECT c1 FROM t WHERE c1 BETWEEN 10 and 20 FOR UPDATE;
    (A-3) COMMIT;
    ```

    (A-1)번 쿼리가 실행된 경우, 당연히 쿼리 결과는 `t.c1 = 13`인 row와 `t.c1 = 17`인 row 2개일 것이다. 그렇다면 lock은 어떻게 걸려있을까? READ COMMITTED transaction은 record lock만 걸고 gap lock은 사용하지 않는다. 따라서 (1)번 쿼리가 실행된 직후 걸려있는 lock은 `t.c1 = 13`과 `t.c1 = 17`에 대한 record lock이다.

    이 때 transaction B가 `t.c1 = 15`인 row를 삽입하려고 한다((B-1)번 쿼리). Transaction A는 gap lock을 걸지 않았기 때문에 transaction B는 자유롭게 `t.c1 = 15`인 row를 삽입할 수 있다. 이 상태에서 transaction B는 commit 했다((B-2)번 쿼리).

    이제 다시 transaction A가 (A-2)번 query를 실행한다. 그러면 transaction A의 isolation level은 READ COMMITTED이기 때문에 새롭게 snapshot을 갱신해온다. 이 과정에서 transaction B가 삽입한 `t.c1 = 15`인 row를 읽어 들인다. 이것이 바로 phantom row이다.

    만약 transaction A의 isolation level이 REPEATABLE READ이었다고 하자. 그러면 (B-1)번 쿼리가 실행될 때 `t.c1 = 15`인 gap에 gap lock이 걸려있었을 것이다. 따라서 transaction B는 transaction A가 commit 되어 lock을 해제할 때까지 기다리고, phantom read는 일어나지 않는다. 즉, 내가 업데이트 하려는 `10 <= t.c1 <= 20`에 해당하는 row의 후보군이 변화하는 일이 없다.

## 4. Serializable

Serializable 트랜잭션은 기본적으로 Repeatable Read와 동일하다. 대신, `SELECT` 쿼리가 전부 `SELECT ... FOR SHARE`로 자동으로 변경된다.(단, AUTOCOMMIT이 꺼져있을 때만 그렇다.) 즉, 읽기 작업을 할 때 조차 Lock을 걸기 때문에 동시에 접근할 수가 없어진다. Serializable은 Dirty Read, Non-Repeatable Read, Phantom Read라는 부정합은 발생하지 않지만, 동시성이 낮아져서 효율이 안 좋아진다. 

이는 Repetable Read에서 막을 수 없는 몇 가지 상황을 방지할 수 있다. 예를 들어, 각 트랜잭션을 모두 Serializable로 실행한다고 가정하자. 

```sql
(A-1) SELECT state FROM account WHERE id = 1;
(B-1) SELECT state FROM account WHERE id = 1;
(B-2) UPDATE account SET state = ‘rich’, money = money * 1000 WHERE id = 1;
(B-3) COMMIT;
(A-2) UPDATE account SET state = ‘rich’, money = money * 1000 WHERE id = 1;
(A-3) COMMIT;
```

우선, (A-1)번 `SELECT` 쿼리가 `SELECT ... FOR SHARE`로 바뀌면서 `id = 1` 인 row에 Shared lock이 걸린다. 그리고 (B-1)번 `SELECT` 쿼리 역시 `id = 1`인 row에 Shared lock을 건다. 그 상황에서 transaction A와 B가 각각 2번 `UPDATE` 쿼리를 실행하려고 하면 row에 Exclusive lock을 걸려고 시도할 것이다. 하지만 이미 해당 row에는 Shared lock이 걸려있다. 따라서 Deadlock 상황에 빠지고, 두 transaction 모두 timeout으로 실패할 것이다. 따라서 money는 1로 안전하게 남아있다.

이 경우에서 알 수 있듯이, `SERIALIZABLE` isolation level은 데이터를 안전하게 보호할 수는 있지만 **굉장히 쉽게 Deadlock에 걸릴 수 있다**. 따라서 `SERIALIZABLE` isolation level은 deadlock이 걸리지 않는지 신중하게 계산하고 사용해야 한다.

- **참고) Serializable이 아니면 UPDATE, DELETE에 주의해라.**

    한 가지 주의해야 할 점은 `**UPDATE`나 `DELETE`는 consistent read의 적용을 받지 않는다**는 것이다. 즉, 같은 `WHERE` 조건을 사용하더라도, '내가 수정하려고 `SELECT` 쿼리로 읽어온 row'와 '해당 row들을 수정하기 위해 `UPDATE` 쿼리를 날렸을 때 실제로 수정되는 row'가 다를 수 있다.

    한 가지 예를 들어보겠다. 다음과 같이 2개의 `REPEATABLE READ` 트랜잭션이 실행된다고 가정하자. 

    ```sql
    (Transaction A - REPETABLE READ)
    (A-1) SELECT COUNT(c1) FROM t WHERE c1 = 'xyz';
    (A-2) DELETE FROM t WHERE c1 = 'xyz';
    (A-3) COMMIT;
    ```

    ```sql
    (Transaction B - REPETABLE READ)
    (B-1) INSERT INTO t(c1, c2) VALUES('xyz', 1), ('xyz', 2), ('xyz', 3);
    (B-2) COMMIT;
    ```

    두 transaction이 다음과 같은 순서로 실행되었다고 해보자.

    ```sql
    (A-1) SELECT COUNT(c1) FROM t WHERE c1 = 'xyz'; // 0
    (B-1) INSERT INTO t(c1, c2) VALUES('xyz', 1), ('xyz', 2), ('xyz', 3);
    (B-2) COMMIT;
    (A-2) DELETE FROM t WHERE c1 = 'xyz'; // 3 rows deleted
    (A-3) COMMIT;
    ```

    처음에 테이블 `t`가 비어있었다면 (A-1)번 쿼리의 의 실행 결과는 0이다. 이 때 실행된 쿼리는 non-locking `SELECT` 쿼리이므로 lock은 걸려있지 않다. 덕분에 transaction B는 자유롭게 `t.c1 = 'xyz'`인 row를 삽입할 수 있다. 따라서 분명 (A-1)번 쿼리에서는 `t.c1 = 'xyz'`인 row가 0개였고 같은 `WHERE` 조건으로 `DELETE` 쿼리를 실행했음에도 불구하고 (A-2)번 쿼리는 3개의 row를 삭제한다.

    만약 위 상황처럼 **consistent read에는 보이지 않는 row에 `UPDATE`와 `DELETE` 쿼리로 영향을 준 경우, 그 시점 이후로는 해당 row가 transaction에서 보이기 시작**한다.

    이 상황에 대한 예를 들어보자. 트랜잭션 A가 다음과 같은 쿼리를 실행한다고 가정해보자. 

    ```sql
    (Transaction A - REPEATABLE READ)
    (A-1) SELECT COUNT(c1) FROM t WHERE c1 = 'abc'; // 0
    // (트랜잭션 B에서 WHERE c2 = 'abc'에 해당하는 데이터를 3개 넣었다고 가정하자.)
    (A-2) UPDATE t SET c1 = 'cba' WHERE c1 = 'abc'; // 3 rows updated
    (A-3) SELECT COUNT(c1) FROM t WHERE c1 = 'cba'; // 3
    ```

    그리고 transaction B가 (1)번과 (2)번 쿼리 사이에 `c2 = 'abc'`인 row를 몇 개 삽입했다고 하자. 그러면 아까 전 예시와 동일하게 (2)번 쿼리는 (1)번 쿼리에서는 보이지 않았던 row를 수정할 것이다. 그러면 이 순간부터 transaction A에서는 이 row들이 보이기 시작한다. 따라서 (3)번 쿼리의 결과는 3이 된다.

    만약 두 예시에서 transaction isolation level이 `SERIALIZABLE`이었다면 어땠을까? 앞서 모든 `SELECT` 쿼리는 `SELECT ... FOR SHARE`로 자동으로 변경된다고 했다. 따라서 두 예시 모두에서 (A-1)번 쿼리는 record S lock을 걸었을 것이다. 그러면 transaction B에서 `UPDATE` 쿼리를 실행할 때 Exclusive lock을 걸려고 할 때, 이미 해당 record에는 Shared lock이 걸려있으므로 수정되지 않고 대기 상태로 빠질 것이다.(이 부분은 `t.c1` column에 index가 걸려있는 상황을 가정한 상황이다. 하지만, 필자가 index가 걸려있지 않은 column에 대해서 테스트 했을 때에도 transaction B가 대기 상태로 빠졌다. 이때 걸려있는 lock을 확인해보니, transaction A가 primary key 때문에 생성된 index에 lock을 건 것을 확인할 수 있었다. 추측하건대, WHERE 조건에 index가 없는 column만 포함될 locking SELECT를 할 경우 해당하는 row의 primary key index에 lock을 거는 것으로 보인다.) 따라서 transaction A의 (A-2)번 쿼리는 안전하게 (1)번 쿼리에서 본 row만 수정한다.

# References

[Lock으로 이해하는 Transaction의 Isolation Level](https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/)