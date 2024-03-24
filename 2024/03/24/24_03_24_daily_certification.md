# 24_03_23_daily_certification

# 데이터베이스

## Transaction

### 예시

**Transaction**은 데이터베이스에서 매우 중요한 개념이다.

A의 계좌에 100만원, B의 계좌에 200만원이 있다고 가정하자.

**A가 B에게 20만원을 이체**하려고 할 때 DB 상에서 A, B의 계좌에 어떤 일이 발생할까?

1. A의 계좌에서 20만원이 출금되어 80만원만 남는다.
2. B의 계좌에 20만원이 입금되어 220만원이 된다.

1, 2를 SQL 쿼리로 나타내면 다음과 같다.

**SQL 1**

```sql
UPDATE account SET balance = balance - 200000 WHERE id = 'A';
```

**SQL 2**

```sql
UPDATE account SET balance = balance + 200000 WHERE id = 'B';
```

이체 작업은 1, 2 작업이 둘 다 일어나야 성공한다.

만약 이체가 실패할 경우(은행이 점검 시간이라던지), 1, 2 둘 다 일어나서는 안된다.

1, 2 둘 중 하나만 일어난다면 이는 이체가 실패하는 것보다 더 큰 문제이다. 

A 계좌에서 20만원이 출금되었는데 B는 받지 못한다거나, 반대로 A 계좌에서 출금이 일어나지 않았음에도 B가 20만원을 받는다면 이는 매우 큰 문제이다.

**따라서 이체 위의 두 SQL 쿼리는 둘 다 성공하거나 둘 다 실패해야 한다.**

이체 작업은 작업 1, 2가 둘 다 성공해야 하는 **단일 작업**이다. 

**데이터베이스에는 이런 단일 작업을 Transaction이라고 한다.**

### Transcation

- **단일한 논리적인 작업 단위(a single logical unit of work)**
- 논리적인 이유로 여러 SQL문들을 단일 작업을 묶어서 나누어질 수 없게 만든 것
- Transcation을 구성하는 SQL문들 중 **일부만 성공해서 DB에 반영되는 일은 일어나지 않는다.**
    - 모두 성공해서 DB에 반영되거나, 모두 실패해서 어떤 SQL도 DB에 반영되서는 안된다.

### COMMIT

- **현재까지 작업한 내용을 DB에 영구적으로(permanently) 저장한다.**
- transaction을 종료한다.

### **ROLLBACK**

- **현재까지 작업한 내용을 모두 취소하고 Transaction 이전 상태로 되돌린다.**
- transaction을 종료한다.

위의 이체 과정의 SQL을 하나의 Transaction으로 나타내면 아래와 같다.

```sql
START TRANSACTION;
UPDATE account SET balance = balance - 200000 WHERE id = 'A';
UPDATE account SET balance = balance + 200000 WHERE id = 'B';
COMMIT;
```

START TRANSACTION 명령어로 Transaction을 시작하고, COMMIT으로 Transaction을 끝낸다.

위의 예제 대로 A에서 B로 20만원을 이체했을 때 테이블은 다음과 같은 상태이다.

| id | balance |
| --- | --- |
| A | 800000 |
| B | 2200000 |

추가로 A에서 B로 30만원을 더 이체한다고 가정해보자. 다음과 같은 SQL을 작성할 수 있다.

```sql
START TRANSACTION;
UPDATE account SET balance = balance - 300000 WHERE id = 'A';  //  1
UPDATE account SET balance = balance + 300000 WHERE id = 'B';  //  2
COMMIT;
```

쿼리 1이 끝난 상태에서 테이블은 다음과 같다.

| id | balance |
| --- | --- |
| A | 500000 |
| B | 2500000 |

A에서는 30만원이 출금되었지만 아직 B에 입금되지는 않은 상태이다.

만약 1, 2 사이에 다음과 같이 ROLLBACK이 일어난다면

```sql
START TRANSACTION;
UPDATE account SET balance = balance - 300000 WHERE id = 'A';  //  1
ROLLBACK;
UPDATE account SET balance = balance + 300000 WHERE id = 'B';  //  2
COMMIT;
```

| id | balance |
| --- | --- |
| A | 800000 |
| B | 2200000 |

테이블은 START TRANSACTION 이전 상태로 돌아간다.

### AUTOCOMMIT

- 각각의 SQL문을 자동으로 Transaction 처리해준다.
- 각각의 SQL문이 성공적으로 실행되면 자동으로 commit한다.
- 실행 중에 문제가 있었다면 알아서 rollback한다.
- MySQL에서는 defalut로 enablede되어 있다.
    
    ```sql
    SELECT @@AUTOCOMM;  // AUTOCOMMIT 여부 확인
    ```
    
- **MySQL에서는 START TRANSACTION 이후의 쿼리들은 자동으로 AUTOCOMMIT이 off가 된다.**
    
    COMMIT/ROLLBACK으로 Transaction이 종료되면 AUTOCOMMIT이 기존의 설정대로 돌아간다.
    

### 일반적인 Transaction 사용 패턴

1. Transaction을 시작(begin)한다. (START TRANSACTION)
2. 데이터 삽입, 수정, 삭제 등의 SQL문들을 포함한 로직을 수행한다.
3. 2의 과정들이 문제없이 동작했다면 Transaction을 commit한다.
4. 2의 과정 중간에 문제가 발생했다면 transaction을 rollback한다.

하지만 Back-End 개발자가 직접 SQL문으로 Transaction을 관리할 일은 잘 없다. 

대신 Java 같은  Programming 언어로 Transaction을 관리하게 된다.

```java
public void transfer(String fromId, String toId, int amount) {
  try {
    Connection connection = ...;           // get DB connection .. (Application Server와 Database Server를 연결)
	  connections.setAutoCommit(false);      // means START TRANSACTION
	  ...                                    // update at fromId (logic)
	  ...                                    // update at toId (logic)
	  connection.commit();                   // logic 성공 시 commit
  } catch(Exception e) {
	  ...
	  connections.rollback();                // logic 실패 시 rollback
	  ...
  } finally {
	  connections.setAutoCommit(true);       // transaction 종료 후 connection 원래 설정 복구
	}
}
```

사실 위의 코드는 이체와 관련된 business logic 코드는 2줄밖에 되지 않는다. 나머지는 전부 transaction 관리 코드이다. Spring, SpringBoot에서는 @Transactional Annotaion을 이용해 transaction 관리 코드를 자동으로 추가해준다.

```java
@Transactional
public void transfer(String fromId, String toId, int amount) {
  ...                                    // update at fromId (logic)
  ...                                    // update at toId (logic)
}
```

### ACID

**Transaction이 반드시 지녀야 하는 핵심 속성이다.**

- **Atomicity (원자성)**
    - **ALL or NOTHING**
    - Transaction은 논리적으로 쪼개질 수 없는 작업 단위이므로 **내부의 SQL문들이 모두 성공**해야 한다.
    - **중간에 SQL문이 실패하면 지금까지의 작업을 모두 취소하**여 아무 일도 없었던 것처럼 rollback한다.
    - DBMS가 담당하는 것
        - commit했을 때 DB에 영구적으로 저장되는 것
        - rollback했을 때 이전 상태로 되돌리는 것
    - 개발자가 담당해야 하는 것
        - 언제 commit할지 정하기 (Transaction의 단위를 정하기)
        - 언제 rollback할지 정하기 (어떤 경우 실패로 처리할지 정하기)
- **Consistency (일관성)**
    - 데이터베이스의 일관성을 유지시키는 것
        - 예를 들어 계좌 잔고로 음수를 허용하지 않는다면 잔액을 초과한 금액의 이체는 실행시키지 않는다.
    - **Transaction은 DB 상태를 consistent 상태에서 또 다른 consistent 상태로 바꿔줘야 한다.**
        - 일관성을 유지하던 DB가 어떤 transaction 후에 일관성이 깨져있을 수는 없다.
    - constraints, trigger 등을 통해 DB에 정의된 rules을 transaction이 위반했다면 해당 transaction을 rollback해야 한다.
    - Transaction이 DB에 저장된 rule을 위반했는지는 DBMS가 commit 전에 확인 후 알려준다.
        - 개발자가 개발을 잘했다면 Exception Message를 통해 확인 가능할 것이다.
        - 하지만 해당 Exception은 DB에 정의된 rule 위반에 관해 알려줄 뿐이다.
    - **Application 관점에서 Transaction이 consistent하게 동작하는지 개발자가 확인해야 한다.**
        - Application의 Business Logic 관점에서도 일관성이 유지되는지는 개발자가 확인해야 한다.
- **Isolation (격리성)**
    - **여러 Transaction들이 동시에 실행될 때도 혼자 실행되는 것처럼 동작하게 만든다.**
    - DBMS에서는 여러 종류의 Isolation Level을 제공한다.
        - Isolation Level이 높을 수록 보다 엄격한 격리성을 유지해서 다른 Transcation으로부터 받는 영향이 줄어든다.
        - 하지만 Isolation Level이 높을 수록 동시에 실행될 수 있는 동시성이 감소해 DB Server의 Performance가 감소한다.
        - Isolation Level이 낮을 수록 동시에 실행될 수 있는 동시성이 증가해 DB Server의 Performance가 증가한다.
        - 하지만 Isolation Level이 높을 수록 다른 Transcation으로부터 받는 영향이 커져 잘못된 결과를 낳을 수 있다.
    - 개발자는 Isolation Level 중에 어떤 Level로 Transaction을 동작시킬지 설정할 수 있다.
        - 디폴트 설정이 있긴 하지만 상황에 따라 튜닝할 수 있어야 한다.
    - Concurrency Control의 주된 목표가 Isolation이다.
- **Durability (지속성, 영존성)**
    - **commit된 Transaction은 DB에 영구적으로 저장한다.**
    - DB System에 power fail, DB crash 같은 문제가 발생해도 commit된 Transaction은 DB에 남아 있어야 한다.
    - ‘영구적으로 저장한다’는 일반적으로 **비휘발성 메모리 (HDD, SSD)에 저장**함을 의미한다.
    - 기본적으로 Transaction의 Durability는 DBMS가 보장한다.

### Transaction과 개발자

1. **Transaction을 어떻게 정의해서 사용할지는 개발자가 정하는 것이다.**
    
    구현하려는 기능과 ACID 속성을 이해해야 Transaction을 잘 정의할 수 있다.
    
2. **Transaction의 ACID와 관련해서 개발자가 챙겨야 하는 부분이 있다.**
    
    DBMS가 모두 알아서 해주는 것이 절대 아니다.