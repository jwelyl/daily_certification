# 24_04_24_daily_certification

# 데이터베이스

# Index

## 문제 제기

**CUSTOMER**

| id | last_name | first_name | address | birth_date |
| --- | --- | --- | --- | --- |
| 1 | … | … | … | … |
| 2 | … | … | … | … |
| … | … | … | … | … |
| 1000000 | … | … | … | … |

위와 같은 테이블에 튜플이 100만개 있을 때, first_name이 Minsoo인 튜플을 찾으려면 다음 쿼리가 필요하다.

```sql
SELECT * FROM customer WHERE first_name = 'Minsoo';
```

인덱스가 걸려있지 않을 경우 **Full Scan(Table Scan)**으로 **O(N)**의 시간 복잡도로 찾아야 한다.

**first_name으로 index가 걸려있다면 B-Tree Based Index 기준 O(logN)의 시간 복잡도로 찾을 수 있다.**

## Index를 사용하는 이유

**조건을 만족하는 튜플들을 빠르게 조회하기 위해 사용한다.**

**빠르게 정렬(order by)하거나 그룹화(group by)하기 위해서도 사용한다.**

```sql
SELECT * FROM customer WHERE first_name = 'Minsoo';

DELETE FROM logs WHERE log_datetime < '2022-01-01';

UPDATE employees SET salary = salary * 1.5 WHERE dept_id = 1001;

SELECT * FROM employees E JOIN department D ON E.dept-id = D.id;
```

WHERE 절의 조건을 만족하는 튜플들, JOIN 조건을 만족하는 튜플들을 빠르게 찾기 위해 인덱스가 필요하다.

## Index 관련 SQL 문법

### 이미 존재하는 테이블에 Index 생성하기

**PLAYER**

| id | name | team_id | backnumber |
| --- | --- | --- | --- |

PLAYER 테이블에 대해 다음과 같은 쿼리가 실행된다고 가정해보자.

```sql
SELECT * FROM player WHERE name = 'Sony';

SELECT * FROM player WHERE team_id = 105 and backnumber = 7;
```

위 쿼리가 효율적으로 실행되기 위해 Index를 걸려면 MySQL 기준으로 다음과 같이 걸어줘야 한다.

**name으로 index 걸기 (name은 중복을 허용해야 함)**

```sql
CREATE INDEX player_name_idx ON player (name);
```

{team_id, back_number}는 candidate key이므로 튜플을 고유하게 식별한다.  

후보키를 index로 걸려면 다음과 같이 걸어야 한다.

**{team_id, back_number}로 index 걸기 (중복을 허용하지 않음)**

```sql
CREATE UNIQUE INDEX team_id_backnumber_idx ON player (team_id, backnumber);
```

### 테이블 정의할 때 Index도 같이 정의하기

다음 PLAYER 테이블을 생성할 때 index도 같이 정의한다.

**PLAYER**

| id | name | team_id | backnumber |
| --- | --- | --- | --- |

```sql
CREATE TABLE player (
	ID          INT          PRIMARY KEY,
	NAME        VARCHAR(20)  NOT NULL,
	team_id     INT,
	backnumber  INT,
	INDEX player_name_idx (name),
	UNIQUE INDEX team_id_backnumber_idx (team_id, backnumber)
);
```

INDEX 이름(player_name_idx, team_id_backnumber_idx)을 생략하면 알아서 만들어준다.

### 테이블에 걸려 있는 Index 조회

**PLAYER**

| id | name | team_id | backnumber |
| --- | --- | --- | --- |

```sql
SHOW INDEX FROM player;
```

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled.png)

PRIMARY KEYf로 만들어진 index, team_id, backnumber로 이루어진 index, name으로 이루어진 index 3개가 존재한다.

### 쿼리가 어떤 Index를 사용하는지 확인하기

**EXPLAIN** 뒤에 쿼리를 적어주면 사용한 index 정보를 알 수 있다.

```sql
EXPLAIN SELECT * FROM player WHERE backnumber = 7;
```

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/5aa4567d-dfc3-4d8e-bc40-d58bd2003f1a.png)

**DBMS의 Optimizer가 알아서 적절하게 Index를 선택해준다.**

### 쿼리가 특정 Index를 사용하도록 명시하기

직접 쿼리에 쓰일 Index를 정해주고 싶을 수도 있다. **USE INDEX 키워드로 사용할 index를 명시한다.**

```sql
SELECT * FROM player USE INDEX(backnumber_idx) WHERE backnumber = 7;
```

그러면 가급적 USE INDEX로 명시한 index를 사용한다.

반드시 명시한 Index를 사용하게 하려면 **FORCE INDEX 키워드로사용할 index를 명시한다.**

```sql
SELECT * FROM player FORCE INDEX(backnumber_idx) WHERE backnumber = 7;
```

그러면 거의 무조건 FORCE INDEX를 사용한다. 정 성능이 안나올거 같으면 Optimizer가 Full Scan을 할 수도 있다.

그 외에도 특정 Index를 제외하는 IGNORE INDEX 키워드도 존재한다.

## Index의 성질

- **조건을 만족하는 튜플들을 빠르게 조회하기 위해 사용한다.**
- **빠르게 정렬(order by)하거나 그룹화(group by)하기 위해서도 사용한다.**
- **{team_id, back_number}와 같은 index를 multicolumn index 또는 composite index라고 한다.**
- **대부분 RDBMS는 Primary Key에 대해서 자동으로 index를 생성해준다.**
- **Foreign Key에는 Index가 자동으로 생성되지 않을 수 있다. MySQL은 자동으로 생성해준다.**
    
    **→ JOIN 관련해서 성능에 영향을 미친다.**
    
- **이미 데이터가 수백만 건 이상 있는 테이블에 Index를 생성할 경우, 시간이 몇 분 이상 소요될 수 있고 DB 성능이 좋지 않을 수 있다. Index 생성 작업은 Traffic이 적을 때 하자.**

## B-Tree 기반 Index 동작 방식

MEMBERS 테이블의 a attribute로 Index를 생성하면 다음과 같다. (실제론 B-Tree 형태이다.)

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%201.png)

**Index는 a의 값들을 정렬된 형태로 가지고 있고, a의 값에 대응되는 MEMBERS 테이블의 튜플을 가리키는 포인터들을 가지고 있다.**

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%202.png)

```sql
WHERE a = 9;
```

a = 9 인 튜플을 찾아서 처리를 한다고 해보자. (SELECT, DELETE, UPDATE 등등)

INDEX에서 a = 9인 튜플을 찾는데 이 때 a에 대해 정렬되어 있으므로 Binary Search로 O(logN)에 찾을 수 있다. a = 9인 튜플을 찾고 해당 포인터를 통해 MEMBERS 테이블의 튜플에 접근이 가능하다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%203.png)

```sql
WHERE a = 7 AND b = 95;
```

a = 7이고, b= 95인 튜플을 찾아서 처리를 한다고 해보자.

index에는 a에 관한 포인터만 존재한다. 일단 a = 7인 튜플들만 찾는다. a = 7인 튜플을 찾아 해당 포인터로 MEMBERS 튜플에 접근하고 해당 튜플의 b 값을 확인해야 한다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%204.png)

a = 7인 튜플을 찾아갔는데 b = 80이므로 찾는 튜플이 아니다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%205.png)

a = 7인 튜플을 찾아갔는데 b = 78이므로 찾는 튜플이 아니다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%206.png)

a = 7인 다른 튜플을 찾아갔는데 b = 95이므로 찾는 튜플이 맞다. 따라서 처리한다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%207.png)

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%208.png)

a에 대해서만 인덱스가 걸려있는데, b에 대한 조건으로도 찾아야 한다면, a에 해당하는 튜플들은 빠르게 찾을 수 있지만 해당 튜플들에 대하여 b에 대한 조건도 만족하는지 확인해야 하므로 결국 Full Scan이 된다.

O(logN)으로 효율적으로 해당 튜플을 찾기 위해서는 a와 b로 생성한 index가 필요하다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%209.png)

Index 테이블은 a, b에 대해 정렬되어 있는데 CREATE INDEX 명령문에 적어준 순서대로 a에 대해 우선적으로 정렬되고, a의 값이 같을 경우에는 b에 대해 정렬된다.

**Multicolumn Index를 생성할 경우 attribute 순서가 중요하다.**

```sql
WHERE a = 7 and b = 95;
```

a와 b 모두 index에 존재하므로 a, b 기준으로 Binary Search를 하면 된다. Binary Search를 할 때, a를 기준으로 먼저 일치하는 튜플을 찾고, a가 일치할 경우 b를 기준으로 일치하는 튜플을 찾는다. a, b에 대해 모두 정렬되어 있으므로 O(logN)에 찾을 수 있다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%2010.png)

하지만 아래 index로 b = 95인 값을 찾으려고 할 경우 성능이 좋지 못하다.

```sql
WHERE b = 95;
```

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%209.png)

index는 a에 대해 정렬되어 있고, a가 같을 경우에만 b에 대해 정렬되어 있기 때문이다.

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%2011.png)

이럴 경우 b에 대한 index를 따로 만들어서 사용해야 좋은 성능을 기대할 수 있다.

**예제**

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%2012.png)

1,2 번째 쿼리는 {team_id, backnumber}로 인덱스가 생성되어 있으므로 빠르게 찾을 수 있다.

하지만 세 번째 쿼리는 backnumber로 튜플을 찾는데 인덱스는 team_id로 우선 정렬되어 있으므로 Full Scan이 필요하다.

네 번째 쿼리는 team_id = 110 조건을 만족하는 튜플은 빠르게 찾을 수 있지만 backnumber = 7을 만족하는 튜플은 Full Scan이 필요하다.

**사용되는 쿼리에 맞춰서 적절하게 index를 걸어줘야 한다.**

**사용하는 쿼리의 성능이 너무 나쁠 경우, index를 걸어주는 것을 고려해야 한다.**

## Index의 주의 사항

### 모든 가능한 column 조합에 대해 Index를 만들면 좋지 않을까? → No!!

1. **테이블에 write할 때마다 Index도 변경된다.**
    
    테이블에 새로 데이터를 입력하면 모든 Index에도 해당 tuple을 저장해야 한다.
    
    B-Tree의 구조를 새로 조정해야 하므로 그에 따른 Overhead가 존재한다.
    
    Index가 많아질 수록 그 Overhead는 커진다.
    
2. **Index 자체도 추가적인 저장 공간을 차지한다.**
    
    Index를 위한 데이터가 존재하므로 Index가 많아질 수록 저장 공간도 많이 필요하다.
    

**따라서 불필요한 Index를 많이 만들면 좋지 않다.**

ex) {team_id, backnumber} index가 존재하는데 team_id로 index를 또 만든다던지

## Covering Index

**조회하려는 attributes를 index가 모두 cover하는 index이다.**

**조회 성능이 더 빠르다.**

![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%2013.png)

{team_id, backnumber}로 이루어진 index가 존재하고 쿼리에서 가져오려는 값이 team_id, back_number일 경우 굳이 PLAYER 테이블에 접근할 필요 없이 index에 존재하는 값을 가져오면 된다. 실제 테이블에 접근할 필요가 없으므로 조회 성능이 더 빠르다.

조회 성능을 위해 의도적으로 Covering Index를 만들기도 한다.

## Hash Index

**B-Tree Index에 비해 장점도 있지만 단점이 뚜렷해서 많이 쓰이진 않는다.**

**Pros**

- Hash Table을 사용하여 Index를 구현한다.
- 조회의 시간복잡도 O(1)의 성능을 가진다.

**Cons**

- Rehashing이 부담스럽다.
    - Rehashing : Hash는 Array로 구현되는데 데이터가 늘어날 수록 Array 크기를 늘려줘야 한다.
    - Traffic이 심할 경우 테이블 사이즈를 키우는 것이 부담스러울 수 있다.
- Hash Table을 사용하므로 Equality 비교만 가능하고 Range 비교는 불가하다.
- Multicolumn Index를 만들 경우 index의 전체 attributes에 대한 조회만 가능하다.
    - {a, b}로 index를 만들 경우 B-Tree 기반 index에서는 a로만 조회가 가능하지만 Hash Index는 불가

## Full Scan이 더 좋을 경우

- 테이블의 데이터가 적을 경우 (수십 ~ 수백건 정도)
- **조회하려는 데이터가 테이블의 상당 부분을 차지할 때**
    
    ![Untitled](24_04_24_daily_certification%20a7658437f4b74c589e19db1cf0890f3b/Untitled%2014.png)
    
    전체 고객이 100만명인데 SK를 사용하는 고객이 수십만명이 넘어갈 경우, Full Scan이 빠르다.
    
- Full Scan이 좋을지, Index로 조회하는 것이 좋을지도 Optimizer가 판단한다.

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 연속 부분 합의 최댓값 구하기 2**

[https://www.codetree.ai/missions/8/problems/max-of-partial-sum-2/description](https://www.codetree.ai/missions/8/problems/max-of-partial-sum-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/max-of-partial-sum-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;
    private static int[] nums;

    private static int start = 0;
    private static int end = 0;
    private static int sum = 0;
    private static int maxSum = Integer.MIN_VALUE;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        nums = new int[n];
        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            nums[i] = Integer.parseInt(tokens.nextToken());

        sum = nums[0];
        maxSum = Math.max(maxSum, sum);

        while(end < n) {
            if(sum < 0) {   //  합이 음수가 될 경우
                if(start < n - 1 && end < n - 1) {;
                    start++;
                    sum = nums[end + 1];
                    end++;
                }
                else break; //  더 이상 갈 수 없을 경우
            }
            else {  //  합이 0 이상일 경우
                end++;
                if(end < n)
                    sum += nums[end];
            }

            maxSum = Math.max(maxSum, sum);
        }

        System.out.println(maxSum);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 쪼개어 배낭 채우기 구현**

[https://www.codetree.ai/missions/8/problems/implement-fractional-knapsack/description](https://www.codetree.ai/missions/8/problems/implement-fractional-knapsack/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/implement-fractional-knapsack/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  보석 개수
    private static int m;   //  가방 무게

    private static final List<Gem> gemList = new ArrayList<>();

    private static double maxValue = 0.0;

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());

        for(int i = 0; i < n; i++) {
            tokens = new StringTokenizer(br.readLine());

            gemList.add(new Gem(Integer.parseInt(tokens.nextToken()), Double.parseDouble(tokens.nextToken())));
        }

        Collections.sort(gemList);

        for(Gem gem : gemList) {
            int w = gem.w;
            double v = gem.v;
            double partial = v / w;

            if(w < m) {
                maxValue += v;
                m -= w;
            } else {
                maxValue += (v / w) * m;
                break;
            }
        }

        System.out.printf("%.3f\n", maxValue);
    }   //  main-end

    private static class Gem implements Comparable<Gem> {
        int w;
        double v;

        public Gem(int w, double v) {
            this.w = w;
            this.v = v;
        }

        @Override
        public int compareTo(Gem gem) {
            return Double.compare(gem.v / gem.w, this.v / this.w);
        }
    }
}   //  Main-class-end
```

**CO{)E TREE 숫자 합치기**

[https://www.codetree.ai/missions/8/problems/merge-numbers/description](https://www.codetree.ai/missions/8/problems/%08merge-numbers/description)

[https://www.codetree.ai/missions/8/problems/merge-numbers/description](https://www.codetree.ai/missions/8/problems/%08merge-numbers/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer tokens;

    private static int n;   //  수 개수

    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();

    private static int ans = 0;

    public static void main(String[] args) throws IOException {
        n = Integer.parseInt(br.readLine());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
            pq.offer(Integer.parseInt(tokens.nextToken()));

        while(pq.size() >= 2) {
            int num1 = pq.poll();
            int num2 = pq.poll();

            ans += (num1 + num2);
            pq.offer(num1 + num2);
        }

        System.out.println(ans);
    }   //  main-end
}   //  Main-class-end
```