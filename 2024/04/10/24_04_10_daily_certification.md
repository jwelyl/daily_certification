# 24_04_10_daily_certification

# 데이터베이스

## Transaction

### **Transaction Isolation**

여러 Transaction들이 동시에 실행되더라도 각각의 Transaction들이 혼자 실행된 것처럼 동작해야 한다.

### 여러 Transaction이 동시에 실행될 때의 이상 현상 (Isolation 위반 사례)

### Dirty Read

**commit되지 않는 변화를 읽음**

**예시**

초기값은 x = 10, y = 20이다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled.png)

Transaction 1에서 y를 읽을 때, Transaction 2에서 commit되지 않은 y = 70 write 결과를 읽는다. 

만약 Transaction 2를 rollback할 경우 Transaction 1은 유효하지 않은 결과를 읽어서 결과를 생성하므로 Transaction 1의 결과에도 문제가 발생한다.

### Nonrepeatable Read (Fuzzy Read)

같은 데이터의 값이 달라짐

**예시**

초기값은 x = 10이다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%201.png)

Transaction 1에서 x를 두 번 읽는데 처음 읽을 때와 두 번째 읽을 때의 결과가 다르다. Transaction 2의 영향을 받은 것인데 이는 Transaction의 ACID 원칙 중 Isolation을 위배한 것이다. Isolation에 따르면 Transaction 1에서 x를 처음 읽었을 때와 두 번째 읽었을 때 모두 같은 값을 읽어야 한다.

### Phantom Read

없던 데이터가 생김

**예시**

초기값은 t1(…, v = 10), t2(…, v = 50)이다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%202.png)

Transaction 1에서 x를 두 번 읽는데 처음 읽었을 때는 tuple t1만 읽혔는데 Transaction 2가 실행된 후 두 번째로 읽었을 때는 tuple t1, t2가 읽히게 된다. 처음 읽었을 때는 없던 데이터가 생겼다.

Dirty Read, Non-repeatable Read, Phantom Read 모두 발생하지 않는 것이 좋다.

하지만 모두 발생하지 않게 하려면 제약사항이 많아져서 동시 처리 가능한 **Transaction 수가 줄어들게 된다.**

**결국 Database의 전체 처리량(Throughput)이 감소하게 된다. (성능이 떨어진다.)**

일부 이상 현상을 허용하는 몇 가지 Level을 만들어서 사용자 필요에 따라 적절히 선택할 수 있다.

이러한 Level을 Isolation Level이라고 한다.

### Transaction Isolation Level (Standard SQL 92 Isolation Level)

**Transaction Isolation Level의 각  level에 따라 허용하는 이상 현상이 다르다.**

Application 개발자는 Isolation Level에 따라 Throughput과 데이터 일관성 사이에 Trade를 한다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%203.png)

- **Read Uncommited**
    - Dirty Read, Non-Repeatable Read, Phantom Read 세 가지를 모두 허용한다.
    - 동시성이 가장 높아서 처리량은 가장 높다.
    - 이상 현상에 매우 취약하다.
- **Read Commited**
    - Dirty Read는 허용하지 않고, Non-Repeatable Read, Phantom Read를 허용한다.
- **Repeatable Read**
    - Dirty Read, Non-Repeatable Read는 허용하지 않고, Phantom Read는 허용한다.
- **Serializable**
    - Dirty Read, Non-Repeatable Read, Phantom Read 세 가지를 모두 허용하지 않는다.
    - 동시성이 없어서 처리량이 매우 낮다.
    - 어떤 이상 현상도 존재하지 않는다.

하지만 위의 Standard SQL 92 Isolation Level은 실제로 발생할 수 있는 더 많은 이상 현상을 반영하지 않았다는 비판이 있다.

### Dirty Write

**commit되지 않는 데이터를 write함**

**예시**

초기 x는 0이다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%204.png)

위 상황에서 Transaction 1을 rollback하면 x는 초기값인 0이 된다. Transaction 2가 실행되지 않은 것처럼 된다. 만약 Transaction 2가 commit된 후에도 Transaction 1이 rollback된다면, x는 0이 되어 Transaction 2는 commit됐음에도 실행되지 않은 것과 같은 결과를 낸다.

**Rollback 시 정상적인 Recovery는 매우 중요하다.**

**따라서 모든 Isolation Level에서 Dirty Write를 허용해서는 안된다.**

### Lost Update

**업데이트를 덮어 씀**

**예시**

초기 x는 50이다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%205.png)

Transaction 1의 commit 결과 Transaction 2의 업데이트 내역은 반영되지 않는다. 매우 심각한 오류다.

계좌 이체라고 생각해보면 50만원 입금하고 150만원 입금했는데 50만원만 입금된 것과 같다.

### Dirty Read

**commit되지 않는 변화를 읽음**

**예시**

초기 x = 50, y = 50이다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%206.png)

x에서 y로 이체하므로 결국 x, y의 합은 100이 유지되어야 한다. 하지만 Transaction 2의 결과 x = 10, y = 50이므로 x + y = 60이 되어 정합성이 깨진다. Abort가 되지 않아도 Dirty Read가 가능하다.

### Read Skew

**inconsistent한 데이터를 읽음**

**예시**

Dirty Read와 같은 예시를 다른 스케쥴로 보자.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%207.png)

마찬가지로 Transaction 2의 결과 x = 50, y = 90이므로 x + y = 140이 되어 정합성이 깨진다.

### Write Skew

**inconsistent한 데이터를 씀**

**예시**

초기 x = 50, y = 50이고 x + y ≥ 0 이라는 제약 사항이 있다고 가정하자.

![Write_Skew.png](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Write_Skew.png)

Transaction 1을 실행할 동안 x + y = 100이므로 x에서 80을 정상적으로 인출하고 commit한다. Transaction 2를 실행할 동안에도 x + y = 100이므로 y에서 90을 정상적으로 인출하고 commit한다. 하지만 결과는 x = -30, y = -40으로 합이 음수가 되어 제약 사항을 위반한다.

Serialized Schedule이었으면 최종 commit할 때 제약 사항을 위반하므로 abort됐겠지만 이미 두 Transaction 모두 commit되었으므로 그럴 수도 없다

### Phantom Read

없던 데이터가 생김

![Phantom_Read2.png](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Phantom_Read2.png)

**예시**

초기값은 t1(…, v = 7)이고 cnt = 0이다. cnt는 v > 10인 tuple 개수이다.

Transaction 1에서 v > 10인 데이터를 읽을 때는 해당하는 tuple이 없었다. 하지만 Transaction 2 실행 후 cnt를 읽을 때는 v > 10인 튜플이 존재하므로 cnt = 1이 된다.

같은 데이터를 여러번 읽는게 아니더라도 연관된 데이터를 읽었을 때 정합성이 깨지는 것도 Phantom Read이다.

### SNAPSHOT ISOLATION

**Concurrency Control의 동작 방식에 따라 정의된 Isolation Level이다.**

Transaction이 시작되는 시점을 Snapshot으로 관리한다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%208.png)

Transaction 1 실행 시작 시점 Snapshot

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%209.png)

40을 이체한 x의 결과를 DB가 아닌 Snapshot에 쓴다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%2010.png)

Transaction 2의 실행 시작 시점의 Snapshot

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%2011.png)

100을 입금한 y의 결과를 DB가 아닌 Snapshot에 쓴다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%2012.png)

Transaction 2가 commit했으므로 commit한 결과를 DB에 적용한다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%2013.png)

Transaction 1이 y를 읽을 경우 Transaction 1 시작 시점의 Snapshot에서 읽게 되므로 150이 아닌 50을 읽게 된다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%2014.png)

Transaction 1이 y에 40을 더한 값 90을 snapshot에 쓴다.

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%2015.png)

Transaction 1을 commit하려 할 때 Transaction 1과 Transaction 2는 같은 y에 대해 write하므로 충돌이 일어난다. 만약 Transaction 1의 결과를 반영한다면 Transaction 2의 결과는 사라지는 Lost Update가 발생한다.

하지만 Snapshot Isolation에서는 여러 Transaction들이 같은 데이터에 대해서 write-write conflict가 발생할 때 먼저 commit되고 그 이후 Transaction은 Abort된다.

### Snapshot Isolation

**Transaction 시작 전에  commit된 데이터만 보인다.**

**먼저 commit된 Transaction 결과 외에는 Abort한다.**

![Untitled](24_04_10_daily_certification%2091312b432d2b4f578bac9a3988b882ed/Untitled%2016.png)

### 실무에서의 Isolation Level

MySQL (innoDB)에는 표준 SQL에 정의된 Isolation Level을 사용한다.

Oracle에서는 READ COMMITED와 SERIALIZABLE을 제공한다.