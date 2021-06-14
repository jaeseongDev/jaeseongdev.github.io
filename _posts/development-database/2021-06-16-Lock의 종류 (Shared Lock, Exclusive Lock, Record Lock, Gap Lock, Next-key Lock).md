---
layout: post
title:  "Lock의 종류 (Shared Lock, Exclusive Lock, Record Lock, Gap Lock, Next-key Lock)"
subtitle: "Lock의 종류 (Shared Lock, Exclusive Lock, Record Lock, Gap Lock, Next-key Lock)"
categories: development
tags: database
comments: false
---

# Lock의 종류 (Shared Lock, Exclusive Lock, Record Lock, Gap Lock, Next-key Lock)

# Row-level Lock

InnoDB에서 **Row-level**의 Lock에는 **Shared Lock**과 **Exclusive Lock**으로 2가지 유형이 존재한다. 

### 1. Shared Lock (공유 잠금, S Lock)

Shared Lock은 **특정 Row를 읽을(read) 때 사용**되어지는 Lock이다. **Shared Lock끼리는 동시에 접근이 가능**하다. 즉, **하나의 Row를 여러 트랜잭션이 동시에 읽을 수 있다는 것**이다. 

하지만 **Shared Lock이 설정된 Row에 Exclusive Lock을 사용할 수는 없다.** 즉, 특정 Row를 누가 읽고 있음으로써 Shared Lock이 설정되어 있는데, 다른 사용자가 그 데이터에 쓰기 작업을 하기 위해 Exclusive Lock을 걸 수 없다는 뜻이다.

일반적인 `SELECT` 쿼리는 Lock을 사용하지 않고 DB를 읽어 들인다. 하지만 `SELECT ... FOR SHARE` 등 일부 SELECT 쿼리는 특정 Row를 읽을 때 InnoDB각 각 Row에 Shared Lock을 건다. 

### 2. Exclusive Lock (배타 잠금, X Lock)

Exclusive Lock은 **특정 Row를 변경(write)하고자 할 때 사용**된다. **특정 Row에  Exclusive Lock이 해제될 때까지, 다른 트랜잭션은 읽기 작업을 위해 Shared Lock을 걸거나, 쓰기 작업을 위해 Exclusive Lock을 걸 수 없다.**

Exclusive Lock은 `SELECT ... FOR UPDATE`나 `UPDATE`, `DELETE` 등의 수정 쿼리를 날릴 때 각 Row에 걸리는 Lock이다.

### Shared Lock, Exclusive Lock 특징 정리

- 여러 트랜잭션이 동시에 한 Row에 Shared Lock을 걸 수 있다. 즉, 여러 트랜잭션이 동시에 한 Row를 읽을 수 있다.
- Shared Lock이 걸려있는 Row에 다른 트랜잭션이 Exclusive Lock을 걸 수 없다. 즉, 다른 트랜잭션이 읽고 있는 Row를 수정하거나 삭제할 수 없다.
- Exclusive Lock이 걸려있는 Row에는 다른 트랜잭션이 Shared Lock, Exclusive Lock 둘 다 걸 수 없다. 즉, 다른 트랜잭션이 수정하거나 삭제하고 있는 Row는 읽기, 수정, 삭제가 전부 불가능하다.
- Shared Lock을 사용하는 쿼리끼리는 같은 Row에 접근이 가능하다. 반면, Exclude Lock이 걸린 Row는 다른 어떠한 쿼리도 접근이 불가능하다.

# Record Lock

Record Lock은 Row가 아니라 **DB의 index record에 걸리는 Lock**이다. 여기도 row-level lock과 마찬가지로 **Shared Lock**과 **Exclusive Lock**이 있다. 

Record Lock의 예시를 들어보자. `c1`이라는 column을 가지는 테이블 `t`가 있다고 하자. 이 때 한 트랜잭션에서 밑과 같은 쿼리를 실행했다. 그러면 `t.c1`의 값이 `10`인 index에 Exclusive Lock이 걸린다. 

```sql
(Transaction A)
SELECT c1 FROM t WHERE c1 = 10 FOR UPDATE;
```

이 때, 다른 트랜잭션에서 밑의 쿼리를 실행하려고 하면, `t2.c1 = 10`인 index record에 Exclusive Lock을 걸려고 시도한다. 하지만 해당 index record에는 이미 Transaction A가 이미 Exclusive Lock을 건 상태이다. 따라서 Transaction B는 Transaction A가 commit되거나 rollback이 되기 전까지, `t.c1 = 10`인 row를 삭제할 수 없다. 이는 `DELETE` 뿐만 아니라 `INSERT`나 `UDPATE` 쿼리도 마찬가지다.

```sql
(Transaction B)
DELETE FROM t WHERE c1 = 10;
```

# Gap Lock

Gap Lock은 **DB index record의 gap에 걸리는 Lock**이다. 여기서 **gap이란 index 중 DB에 실제 record가 없는 부분**이다. 예를 들어 설명해보자. 

`id` column만 있는 테이블이 있고, `id` column에 index가 걸려있다고 하자. 현재 테이블에는 `id = 3`인 row와 `id = 7`인 row가 있다. 그러면 DB와 index는 아래 그림과 같은 상태일 것이다. 

![/assets/img/posts/development-database/2021-06-16-Lock%EC%9D%98%20%EC%A2%85%EB%A5%98%20(Shared%20Lock%2C%20Exclusive%20Lock%2C%20Record%20Lock%2C%20Gap%20Lock%2C%20Next-key%20Lock)/Untitled.png](/assets/img/posts/development-database/2021-06-16-Lock%EC%9D%98%20%EC%A2%85%EB%A5%98%20(Shared%20Lock%2C%20Exclusive%20Lock%2C%20Record%20Lock%2C%20Gap%20Lock%2C%20Next-key%20Lock)/Untitled.png)

그러면 현재 `id <= 2`, `4 <= 1d <= 6`, `8 <= id`에 해당하는 부분에는 index record가 없다. 이 부분이 바로 index record의 gap이다. 그리고 Gap Lock은 이러한 gap에 걸리는 Lock이다. 즉, Gap Lock은 해당 gap에 접근하려는 다른 쿼리의 접근을 막는다. Record Lock이 해당 index를 사용하려는 다른 쿼리의 접근을 막는 것과 동일하다. 둘의 차이점이라고 하면, Record Lock은 이미 존재하는 Row가 변경되지 않도록 보호하는 반면, **Gap Lock은 조건에 해당하는 새로운 Row가 추가되는 것을 방지하기 위함**이다. 

Gap Lock에 대한 예시를 살펴보자. c1이라는 column 하나가 있는 테이블 t가 있다. 여기에는 `c1 = 13`, `c1 = 17`이라는 두 Row가 있다. 이 상태에서 한 트랜잭션에서 밑과 같은 쿼리를 실행했다. 

```sql
(Transaction 1)
SELECT c1 FROM t WHERE c1 BETWEEN 10 AND 20 FOR UPDATE;
```

그러면 `t.c1`의 값이 `10`과 `20` 사이 중 실제 record가 없는 부분인 gap에 Lock이 걸린다. 즉, `10 <= id <= 12`, `14 <= id <= 16`, `18 <= id <= 20`에 해당하는 gap에 lock이 걸린다. 이 상태에서 다른 트랜잭션이 `t.c1 = 15`인 row를 삽입하려고 하면, Gap Lock 때문에 트랜잭션 A가 commit 되거나 rollback 될 때까지 삽입되지 않는다. `INSERT` 뿐만 아니라 `UPDATE`, `DELETE` 쿼리도 마찬가지다. Gap은 하나의 index 값일 수도, 여러 index 값일, 혹은 아예 아무 값도 없을 수도 있다. 

# Next-Key Lock

- 범위를 지정한 쿼리를 실행하게되면 실제로는 위에서 각각 설명했던 record lock (찾아진 인덱스 레코드에 대해)과 gap lock(해당하는 인덱스 레코드 사이사이)이 복합적으로 사용된다. 다음 그림을 통해 Next-Key lock이 어떤 것인지를 한눈에 살펴보자.

![https://letmecompile.s3.amazonaws.com/wp/wp-content/uploads/2018/06/next_key_lock.png](https://letmecompile.s3.amazonaws.com/wp/wp-content/uploads/2018/06/next_key_lock.png)

위의 예제에서 만약 `SELECT * WHERE pk > 99 AND pk < 102 FOR UPDATE`를 실행시켰었다면, **97 < pk < 103**의 범위에 대해서 Lock이 걸렸을 것이다 .

# References

[MySQL InnoDB lock & deadlock 이해하기](https://www.letmecompile.com/mysql-innodb-lock-deadlock/)