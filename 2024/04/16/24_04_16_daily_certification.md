# 24_04_16_daily_certification

# 데이터베이스

## Transaction

### MVCC (Multiversion Concurrency Control)

### **Lock-Based Concurrency Control**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/f108f143-1e6b-4431-89a7-9b969768ddf6.png)

- 같은 데이터에 대해서 동시에 read하는 것만 허용한다.
- 한 TX가 실행될 동안 다른 TX가 Block되는 경우가 많으므로 처리량이 줄어들어 성능이 떨어진다.

### MVCC (Multiversion Concurrency Control)

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled.png)

- **같은 데이터에 대해서 동시에 여러 Transaction이 write하는 것을 Block한다.**
- **그 외의 경우에는 동시에 처리가 가능하다.**
- Transaction 처리량이 증가하여 성능이 높아진다.
- **MVCC는 commit된 데이터만 읽는다.**

write의 경우 write_lock을 얻은 후에 실행되므로 write_lock 표기는 생략한다. 

Recoverability를 위해 commit 다음에 unlock하므로 unlock 표기도 생략한다.

(Lock, Unlock은 기본적으로 개발자가 아니라 RDBMS가 내부적으로 구현하고 해준다.)

**예시**

초기 x = 10이다.

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%201.png)

TX 2가 x에 대한 write_lock을 얻고 x = 50을 write한다. 

하지만 commit 되지는 않았으므로 DB에 쓰는 것이 아닌, TX 2만 접근할 수 있는 공간에 x = 50을 write한다.

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%202.png)

다음으로 TX 1이 x를 읽는다. MVCC는 commit된 데이터만 읽으므로 x = 10을 읽는다.

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%203.png)

TX 2가 commit해서 x = 5이 DB에 write되고 x에 대해 unlock한다.

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%204.png)

위와 같은 상황에서 TX 1이 x를 한 번 더 읽는다면 어떤 x를 읽을까? **TX 1의 Isolation Level에 따라 다르다.**

- **Read Committed 일 경우**
    - **Read하는 시간을 기준**으로 그 전에 commit된 데이터를 읽는다.
    - 따라서 x = 50을 읽는다.
    - MySQL, PostgreSQL 모두 Read Committed일 경우 위와 같이 동작한다.
- **Repeatable Read 일 경우**
    - **Transaction 시작 시간 기준**으로 그 전에 commit된 데이터를 읽는다.
    - 따라서 x = 10을 읽는다.
    - RDBMS마다 조금씩 동작이 다를 수 있다.
    - MySQL, PostgreSQL 모두 Repeatable Read일 경우 위와 같이 동작한다.
- **Serializable일  경우**
    - Repeatable Read와 동일하다.
    - 하지만 MySQL과 PostgreSQL에서의 동작이 조금 다르다.
        - MySQL에서는 Lock으로 동작한다.
        - PostgreSQL에서는 SSI 기법이 적용된 MVCC로 동작한다.
        - 자세한 것은 후술
- **Read uncommitted일 경우**
    - 보통 MVCC가 적용되지 않는다.
        - MVCC는 commit된 데이터를 읽는다.

### PostgreSQL vs MySQL

**예시**

초기 x = 50, y = 10이고 x가 y에 40을 이체하는 TX 1, x에 30을 입금하는 TX 2가 존재한다. 

정상적으로 동작했을 때 최종 결과는 x = 40, y = 50이다.

### PostrgreSQL

**TX 1, 2 모두 Read Committed일 때 PostgreSQL**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%205.png)

TX 1이 x = 50을 읽고, x = 10을 쓴다. 

TX 2는  commit된 x를 읽으므로 x = 50을 읽고, x = 80을 쓰려고 하지만 TX 1이 x에 대한 write_lock을 가지고 있으므로 block된다.

TX 1이 y = 10을 읽고, y = 50을 쓴 뒤에 commit한다.

TX 2는 x에 대한 write_lock을 얻고 x = 80을 쓴 뒤에 commit한다.

최종 결과는 x = 80, y = 50이다. **LOST UPDATE가 발생했다.**

MySQL, PostgreSQL 모두 동일하다.

Isolation Level을 Repeatable Read로 바꾸면 해결된다.

**TX 1은 Read Committed, TX 2는 Repeatable Read일 때 PostgreSQL**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%206.png)

PostgreSQL에서는 TX 1에서 x를 먼저 업데이트하고 commit되므로 나중에 x에 write하려 하는 TX 2는 rollback된다. 따라서 결과는 TX 1만 실행되서 x = 10, y = 50으로 제대로 된 결과가 된다.

TX마다 서로 다른 Isolation Level을 줄 수 있다.

**TX 1은 Read Committed, TX 2는 Repeatable Read일 때 PostgreSQL**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%207.png)

역시 LOST UPDATE가 발생했다. TX 1의 Isolation Level이 Read Committed로는 충분하지 않다.

**TX 1, 2 둘 다 Repeatable Read일 때 PostgreSQL**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%208.png)

PostgreSQL에서는 TX 2에서 x를 먼저 업데이트하고 commit되므로 나중에 x에 write하려 하는 TX 1은 rollback된다. 따라서 결과는 TX 2만 실행되서 x = 80, y = 10으로 제대로 된 결과가 된다.

### MySQL

**TX 1, 2 둘 다 Repeatable Read일 때 MySQL**

MySQL에서는 Repeatable Read만으로는 Lost Update를 해결할 수 없다.

**MySQL에서는 Locking Read로 Lost Update를 해결한다.**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%209.png)

**MySQL의 Locking Read는 가장 최근의 comit된 데이터를 읽는다.**

**Locking Read**

```sql
//  Write Lock (Exclusive Lock)
SELECT ... FOR UPDATE;
//  Read Lock (Shared Lock)
SELECT ... FOR SHARE;
```

**Repeatable Read에서 Write Skew**

초기 x = 10, y = 10이고 TX 1은 x와 y를 더해서 x에 쓰고, TX 2는 x와 y를 더해서 y에 쓴다.

정상적으로 동작했다면 결과는 x = 20, y = 30 또는 x = 30, y = 20이어야 한다.

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%2010.png)

Write Skew가 발생하여 x = 20, y = 20이 된다. 이는 MySQL, PostgreSQL 모두 발생한다.

### **MySQL**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%2011.png)

Locking Read를 통해서 해결할 수 있다.

### PostgreSQL

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%2012.png)

Locking Read를 지원하지만, 먼저 update한 TX가 commit되면 나중 TX는 rollback된다.

### Serializable로 해결

가장 엄격한 TX Isolation Level인 Serializable로 해결 가능하다.

### **MySQL**

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%2013.png)

### PostgreSQL

![Untitled](24_04_16_daily_certification%20c8ebbe2517604f1ebf4c1ffd3a16521b/Untitled%2014.png)

### MVCC 정리

- 데이터를 읽을 때 **특정 시점 기준**으로 **가장 최근에 commit된 데이터를 읽는다.**
    - 특정 시점은 Isolation Level에 따라 다르다.
    - commit된 데이터를 읽는다.
    - MySQL에서는 Consistent Read라고 한다.
- **데이터 변화(write) 이력을 관리**한다.
    - 추가적인 저장 공간을 필요로 한다는 단점이 있다.
- **read와 write가 서로를 block하지 않는다.**
    - read하면서 write도, write하면서 read도 가능하므로 처리량이 높다.
    - 대부분의 RDBMS가 MVCC를 사용해서 구현한다.

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 마라톤 중간에 택시타기**

[https://www.codetree.ai/missions/8/problems/taking-a-taxi-in-the-middle-of-the-marathon/description](https://www.codetree.ai/missions/8/problems/taking-a-taxi-in-the-middle-of-the-marathon/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/taking-a-taxi-in-the-middle-of-the-marathon/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  체크 포인트 개수

    private static int[] posX;  //  체크 포인트 x 좌표
    private static int[] posY;  //  체크 포인트 y 좌표

    private static int[] leftX;     //  left x값 저장 배열
    private static int[] leftY;     //  left y값 저장 배열
    private static int[] rightX;    //  right x값 저장 배열
    private static int[] rightY;    //  right y값 저장 배열

    private static int ans = Integer.MAX_VALUE; //  완주 최소 거리

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        posX = new int[n + 1];
        posY = new int[n + 1];

        leftX = new int[n + 1];
        leftY = new int[n + 1];
        rightX = new int[n + 1];
        rightY = new int[n + 1];

        for(int i = 0; i < n; i++) {
            tokens = new StringTokenizer(br.readLine());
            posX[i + 1] = Integer.parseInt(tokens.nextToken());
            posY[i + 1] = Integer.parseInt(tokens.nextToken());
        }

        for(int i = 1; i < n; i++) {
            leftX[i + 1] = leftX[i] + Math.abs(posX[i + 1] - posX[i]);
            leftY[i + 1] = leftY[i] + Math.abs(posY[i + 1] - posY[i]);
        }

        for(int i = n - 1; i >= 1; i--) {
            rightX[i] = rightX[i + 1] + Math.abs(posX[i] - posX[i + 1]);
            rightY[i] = rightY[i + 1] + Math.abs(posY[i] - posY[i + 1]);
        }

        for(int i = 2; i < n; i++)
            ans = Math.min(ans, leftX[i - 1] + Math.abs(posX[i + 1] - posX[i - 1]) + rightX[i + 1] + leftY[i - 1] + Math.abs(posY[i + 1] - posY[i - 1]) + rightY[i + 1]);
        
        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 숫자 빠르게 찾기**

[https://www.codetree.ai/missions/8/problems/find-number-fast/introduction](https://www.codetree.ai/missions/8/problems/find-number-fast/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/find-number-fast/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;
    private static final StringBuilder sb = new StringBuilder();

    private static int n, m;   //  정렬된 수 개수 n, 찾을 수 개수 m

    private static int[] nums;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());
        
        nums = new int[n];

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        Arrays.sort(nums);

        for(int i = 0; i < m; i++)
            sb.append(binarySearch(Integer.parseInt(br.readLine()))).append("\n");

        System.out.print(sb);
    }   //  main-end

    private static int binarySearch(int target) {
        int idx = -1;   //  찾지 못했을 경우 -1 반환
        int start = 0;
        int end = n - 1;

        while(start <= end) {
            int mid = (start + end) / 2;
            int midNum = nums[mid];
            
            if(midNum == target) {  //  중앙값이 target일 경우
                idx = (mid + 1);
                break;
            }
            else if(midNum < target)    //  중앙값이 target보다 작을 경우
                start = mid + 1;    //  오른쪽에서 찾기
            else                        //  중앙값이 target보다 클 경우
                end = mid - 1;      //  왼쪽에서 찾기
        }

        return idx;
    }
}   //  Main-class-end
```