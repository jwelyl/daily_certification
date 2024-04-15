# 24_04_15_daily_certification

# 데이터베이스

## Transaction

### write-lock

**실제로 데이터베이스에서 write는 생각보다 복잡한 작업이다.** 

- 인덱스가 걸려있다면 인덱스에 대한 처리도 필요하다.
- 실제로 데이터가 저장된 파일에 대한 처리도 필요하다.

하나의 Transaction이 한 데이터에 write하는 동안 다른 Transaction이 해당 데이터에 read/write하려 한다면 문제가 발생할 수 있다.

**이를 방지하기 위해 write_lock을 사용한다.** 

**어떤 Transaction이 데이터에 대해 write_lock을 획득하면 다른 Transaction은 해당 Transaction이 write_lock을 해제할 때까지 해당 데이터에 대해 block된다.**

**예시 (write vs write)**

초기 x = 10이다.

![write_lock_ex1.png](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/write_lock_ex1.png)

Transaction 1이 먼저 x에 대해 write_lock을 얻었으므로 Transaction 2는 x에 대해 write_lock을 얻으려고 시도하지만 Transaction 1이 x에 20을 write하고 unlock할 때까지 block된다. 

Transaction 1이 unlock하면 Transaction 2가 write_lock을 얻고 x에 20을 write한 후 unlock한다.

**예시 (write vs read, write 먼저)**

초기 x = 10이다.

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled.png)

Transaction 1이 먼저 x에 대해 write_lock을 얻었으므로 Transaction 2는 x에 대해 read_lock을 얻으려고 시도하지만 Transaction 1이 x에 20을 write하고 unlock할 때까지 block된다. 

Transaction 1이 unlock하면 Transaction 2가 read_lock을 얻고 x=20을 read한 후 unlock한다.

### write-lock (exclusive lock)

- read/write(insert, modify, delete)할 때 사용한다.
    - read할 때도 사용할 수 있다.
- **다른 Transaction이 같은 데이터를 read/write하는 것을 허용하지 않는다.**

### read-lock (shared lock)

- read할 때 사용한다.
- **다른 Transaction이 같은 데이터를 read하는 것은 허용한다.**

**예시 (write vs read)**

초기 x = 10이다.

![read_write_ex1.png](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/read_write_ex1.png)

Transaction 2가 먼저 x에 대해 read_lock을 얻었으므로 Transaction 1은 x에 대해 write_lock을 얻으려고 시도하지만 Transaction 2가 x=10을 read하고 unlock할 때까지 block된다. 

Transaction 2가 unlock하면 Transaction 1이 write_lock을 얻고 x=20을 write한 후 unlock한다.

**예시 (read vs read)**

초기 x = 10이다.

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%201.png)

Transaction 2가 먼저 x에 대해 read_lock을 얻었지만 Transaction 1은 x에 대해 read_lock을 얻으려고 시도하고 얻을 수 있다.

Transaction 1, 2 모두 동시에 x=10을 읽고 unlock할 수 있다.

### 정리

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/75e193f5-10d1-4a89-a36b-00d01728819e.png)

### Two-Phase Locking Protocol

Lock을 사용하는 것만으로 Transaction의 Serializability를 보장할 수 없다.

**예시**

초기 x = 100, y = 200이다.

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%202.png)

Transaction 1, 2는 각각 위와 같이 동작한다.

두 Transaction이 Serial Schedule로 동작한다면 어떨까?

**Transaction 1 → Transaction 2**

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%203.png)

x = 300, y = 500이 된다.

**Transaction 2 → Transaction 1**

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%204.png)

x = 400, y = 300이 된다.

아래와 같은 Schedule로 동작한다고 가정하자.

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%205.png)

x = 300, y = 300이다. 위의 어떤 Serial Schedule과도 결과가 다르다. 

![nonserializable.png](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/nonserializable.png)

즉, 위의 Schedule은 Nonserializable Schedule이다.

이러한 일이 왜 발생할까?

Transaction 2가 업데이트되지 않은 x를 읽었고 Transaction 1은 업데이트되지 않은 y를 읽었다. Transaction 2가 y에 대한 write를 수행하려고 write_lock을 얻으려고 했지만, Transaction 1이 y에 대한 read_lock을 가지고 있어서 y를 업데이트하지 못했고 Transaction 1이 업데이트되지 않은 y를 읽었다.

아래와 같은 Schedule은 Serial Schedule 2와 같은 결과를 내므로 Serializable Schedule이다.

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%206.png)

각 Transaction에서 write_lock(x)와 unlock(y), write_lock(y)와 unlock(x)의 순서를 바꾸었다.

![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%207.png)

Lock과 관련된 operation만 보면 다음과 같은 특징이 있다.

**Transaction의 모든 Locking Operation이 최초의 Unlock Operation보다 먼저 수행된다.**

이를 **2PL Protocol (Two-Phase Protocol)**이라고 한다.

### 2PL Protocol

**Transaction의 모든 Locking Operation이 최초의 Unlock Operation보다 먼저 수행된다.**

![2pl_protocol.png](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/2pl_protocol.png)

- Expanding Phase (Growing Phase)
    
    Lock을 취득하기만 하고 반환하지는 않는 Phase
    
- Shrinking Phase (Contracting Phase)
    
    Lock을 반환하기만 하고 취득하지는 않는 Phase
    

**2PL Protocol은 Serializbility는 보장한다. 하지만 심각한 문제가 발생할 수 있다.**

### 2PL Protocol Deadlock

![deadlock.png](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/deadlock.png)

Transaction 1은 Transaction 2가 획득한 x의 lock을 unlock하기를 기다리고 있고, Transaction 2는 Transaction 1이 획득한 y의 lock을 unlock하기를 기다리고 있다.

**결국 두 Transaction 모두 서로를 기다리느라 진행되지 못한다.**

Deadlock이 발생하는 이유, Deadlock을 해결하는 방법은 OS의 Deadlock을 참고하자.

### 2PL Protocol의 종류

- **Conservative 2PL**
    - 모든 Lock을 취득한 뒤 Transaction을 시작한다.
        
        ![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%208.png)
        
    - Deadlock이 발생하지 않는다.
    - 실용적이지 않다. Transaction 자체가 시작되기 어려울 수 있다.
- **Strict 2PL (S2PL)**
    - Strict Schedule을 보장한다.
    - Recoverability를 보장한다.
    - write-lock을 commit하거나 rollback할 때 반환한다.
        
        ![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%209.png)
        
- **Strong Strict 2PL (SS2PL or Rigorous 2PL)**
    - Strict Schedule을 보장한다.
    - Recoverability를 보장한다.
    - read-lock / write-lock을 commit하거나 rollback할 때 반환한다.
        
        ![Untitled](24_04_15_daily_certification%2058d72f98a9124025ba90e0cf6fc983b8/Untitled%2010.png)
        
    - S2PL보다 구현이 쉽다.
    - 하지만 read-lock을 가지고 있는 시간이 길어져서 다른 Transaction이 read-lock을 얻기 어렵다.

# Problem Solving (Algorithm & SQL)

**CO{)E TREE Doubly-LinkedList / 연결리스트 1**

[https://www.codetree.ai/missions/8/problems/linked-list1/description](https://www.codetree.ai/missions/8/problems/linked-list1/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/linked-list1/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  연산 개수

    public static void main(String[] args) throws IOException {
        Node cur = new Node(br.readLine());

        n = Integer.parseInt(br.readLine());

        for(int i = 0; i < n; i++) {
            tokens = new StringTokenizer(br.readLine());

            int cmd = Integer.parseInt(tokens.nextToken());

            if(cmd == 1)
                cur.insertPrev(tokens.nextToken());
            else if(cmd == 2)
                cur.insertNext(tokens.nextToken());   
            else if(cmd == 3) {
                Node prev = cur.prev;

                if(prev != null)
                    cur = prev;
            }
            else {
                Node next = cur.next;

                if(next != null)
                    cur = next;
            }

            cur.print();
        }

        System.out.print(sb);
    }   //  main-end

    private static class Node {
        String element;

        Node prev;
        Node next;

        public Node(String element) {
            this.element = element;
            this.prev = null;
            this.next = null;
        }

        public void insertPrev(String element) {
            Node node = new Node(element);
            
            node.next = this;
            node.prev = this.prev;

            if(this.prev != null)
                this.prev.next = node;
            this.prev = node;
        }

        public void insertNext(String element) {
            Node node = new Node(element);
            
            node.prev = this;
            node.next = this.next;

            if(this.next != null)
                this.next.prev = node;
            this.next = node;
        }

        public void print() {
            if(this.prev == null)
                sb.append("(Null) ");
            else
                sb.append(this.prev.element).append(" ");
            
            sb.append(this.element).append(" ");

            if(this.next == null)
                sb.append("(Null) ");
            else
                sb.append(this.next.element).append(" ");
            
            sb.append("\n");
        }
    }    
}   //  Main-class-end
```

**CO{)E TREE 정수 n개의 합 2**

[https://www.codetree.ai/missions/8/problems/sum-of-n-integers-2/introduction](https://www.codetree.ai/missions/8/problems/sum-of-n-integers-2/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/sum-of-n-integers-2/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n = 0;
    private static int k = 0;
    private static int[] nums;
    private static int sum = 0;
    private static int ans = Integer.MIN_VALUE;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());

        n = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        nums = new int[n];

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            nums[i] = Integer.parseInt(tokens.nextToken());

            if(i < k)
                sum += nums[i];
        }

        ans = Math.max(ans, sum);

        for(int end = k; end < n; end++) {
            int start = end - k + 1;

            sum -= nums[start - 1];
            sum += nums[end];

            ans = Math.max(ans, sum);
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 점 개수 세기 3**

[https://www.codetree.ai/missions/8/problems/count-number-of-points-3/introduction](https://www.codetree.ai/missions/8/problems/count-number-of-points-3/introduction)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/count-number-of-points-3/introduction)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;
    
    private static final TreeSet<Integer> treeSet = new TreeSet<>();
    private static final HashMap<Integer, Integer> hashMap = new HashMap<>();

    private static int n, q;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
    
        n = Integer.parseInt(tokens.nextToken());
        q = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            treeSet.add(Integer.parseInt(tokens.nextToken()));

        int idx = 0;
        for(int num : treeSet)
            hashMap.put(num, idx++);

        for(int i = 0; i < q; i++) {
            tokens = new StringTokenizer(br.readLine());
            int from = Integer.parseInt(tokens.nextToken());
            int to = Integer.parseInt(tokens.nextToken());

            sb.append(hashMap.get(to) - hashMap.get(from) + 1).append("\n");
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```