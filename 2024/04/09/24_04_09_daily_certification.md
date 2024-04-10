# 24_04_09_daily_certification

# 데이터베이스

## Transaction

### Recoverability

**Schedule 5**

![concurrency5.jpeg](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/concurrency5.jpeg)

Transaction 1의 r1(A), w1(A) operation 후,  Transaction 2의 r2(B), w2(B) operation이 수행되고. Transaction 1의 r1(B), w1(B) operation이 수행된 후에 Transaction 1을 commit한다. 

**그런데 여기서 Transaction 2를 Abort해서 Rollback하면 어떻게 될까?**

Transaction 2를 rollback했으므로 r2(B), w2(B)의 실행을 취소해야 한다. 

**Transaction 1의 r1(B), w1(B)도 Transaction 2의 operatio의 결과를 읽고, 쓴 결과이므로 유효하지 않다. 따라서 Transaction 1도 rollback해야 한다. 하지만 이미 commit되서 rollback할 수 없다. (Durability)**

### **Unrecoverable Schedule**

schedule 내에서 commit된 Transaction이 rollback된 Transaction이 write했던 데이터를 읽은 경우 이를 **Unrecoverable Schedule**이라고 한다.

Unrecoverable Schedule은 rollback해도 이전 상태로 회복이 불가능하므로 DBMS가 허용해서는 안된다.

위의 예제에서  Transaction 2는 rollback해서 B의 잔고는 200만원이 되었지만 Transaction 1은 rollback할 수 없어서 A의 잔고는 80만원이 된다. **데이터 불일치가 발생한다.**

### Recoverable Schedule

어떤 두 Transaction에 의존성이 존재할 경우 의존되는 Transaction이 commit되거나, rollback될 때까지 의존하는 Transaction은 commit되거나 rollback되서는 안된다.

이러한 Schedule을 **Recoverable Schedule**이라고 하며 DBMS는 이런 Schedule만 허용한다.

![recoverable_schedule1.jpeg](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/recoverable_schedule1.jpeg)

Transaction 1이 Transaction 2를 의존하므로 Transaction 2가 먼저 commit되고 Transaction 1이 commit되어야 한다.

![recoverable_schedule2.jpeg](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/recoverable_schedule2.jpeg)

Transaction 1이 Transaction 2를 의존하므로 Transaction 2가 먼저 rollback되고 Transaction 1이 rollback되어야 한다.

Transaction 2가 rollback되면  Transaction 2에 의존하는 Transaction 1도 같이 rollback되어야 한다.

이렇게 하나의 Transaction이 rollback될 때, 의존하는 다른 Trnasaction도 연쇄적으로 rollback되는 것을 **Cascading Rollback**이라고 한다.

Cascading Rollback은 처리하는 비용이 많이 든다.

**데이터를 write한 Transaction이 commit/rollback한 다음에 데이터를 읽는 Schedule만 허용하자.**

![cascading.jpeg](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/cascading.jpeg)

만약 Transaction 2를 rollback해도 Transaction 2의 write의 결과에 의존하는 Transaction이 없기 때문에 Transaction 2만 rollback하면 된다.

![cascading2.jpeg](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/cascading2.jpeg)

Transaction 2를 rollback해도 Transaction 1을 rollback할 필요 없이 Transaction 1은 정상적으로 수행되는 것을 알 수 있다.

이러한 Schedule을 **Cascadeless Schedule**이라고 한다.

Schedule 내에서 어떤 Transaction도 commit되지 않은 Transaction들이 write한 데이터를 읽지 않는다.

Cascadeless Schedule는 항상 문제가 없을까? 아니다. 다음 예시를 보자.

피자 가게 사장인 A가 한 판에 3만원인 피자 가격을 2만원으로 낮추려 하는데, 직원인 B가 동일한 피자 가격을 실수로 1만원으로 낮추려 한다.

아래와 같은 Schedule로 가격을 낮추려 한다.

![pizza.jpeg](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/pizza.jpeg)

위 Schedule은 Cascadeless Schedule이다. 어떤 Transaction도 commit되지 않은 Transaction이 write한 데이터를 읽는 경우는 없기 때문이다. 그렇다면 문제가 없을까?

직원 B가 실수한 것을 깨닫고 Transaction 1을 rollback할 경우, Transaction 2의 결과도 사라진다.

위와 같은 문제를 방지하기 위해 **Cascadeless Schedule**를 좀 더 보강해야 한다.

**어떤 Transaction도 commit되지 않은 Transaction들이 write한 데이터를 읽지도, 쓰지도 않아야 한다.**

이러한 Schedule을 **Strict Schedule**이라고 한다.

![strict_schedule.jpeg](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/strict_schedule.jpeg)

rollback이 쉽다.

**Recoverable, Cascadeless, Strict Schedule 관계**

![Untitled](24_04_09_daily_certification%202dea1b0d8be64be49d1c2ffb2edc55d2/Untitled.png)

**Concurrency Control은 Serializability와 Recoverability를 제공한다.**

Concurrency Control이 보장하는 Transaction의 성질이 **Isolation**이다.