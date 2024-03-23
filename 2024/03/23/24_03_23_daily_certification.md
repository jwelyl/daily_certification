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
    

# Problem Solving (Algorithm & SQL)

네이버 코딩 테스트 쉽지 않았다. 준비했던 유형과 너무나도 다른 문제만 나와서 아쉬웠다.

천운이 따라줘서 이후 전형으로 갈 수 있다면 좋겠지만, 여기서 멈추게 된다면 서류 준비하면서 아쉬웠던 점들을 보완하면서 후에 지원할 수 있도록 해야겠다.