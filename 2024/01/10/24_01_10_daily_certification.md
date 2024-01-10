# 24_01_10_daily_certification

# JPA

## Entity Manager Factory & Entity Manager

![Untitled](24_01_10_daily_certification%2032eba37093aa4604b20a423765ca89c5/Untitled.png)

## Persistence Context

Entity를 영구히 저장하는 환경

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
EntityManager em = emf.createEntityManager();

...

em.persist(entity);  // 영속성 컨텍스트에 entity 저장
```

Entity Manager를 통해 Persistence Context에 접근

![Untitled](24_01_10_daily_certification%2032eba37093aa4604b20a423765ca89c5/Untitled%201.png)

## Entity Lifecyle

![Untitled](24_01_10_daily_certification%2032eba37093aa4604b20a423765ca89c5/Untitled%202.png)

## Persistence Context의 이점

### 1차 캐시

![Untitled](24_01_10_daily_certification%2032eba37093aa4604b20a423765ca89c5/Untitled%203.png)

Entity 조회 시 우선 Entity Manager의 1차 캐시에서 조회

영속성 컨텍스트에 존재하지 않을 시 DB에서 조회 후 1차 캐시에 저장

 

### 영속 엔티티의 동일성 보장

```java
Member memberA = em.find(Member.class, "member1");
Member memberB = em.find(Member.class, "member1");

System.out.println(memberA == memberB);  // 동일성 비교 true
```

Application 차원에서 반복 가능한 읽기(REPEATABLE READ) 등급의 트랜잭션 격리 수준 제공

Java Collection에서 가져올 때처럼 동일 객체를 가져올 수 있음

### 트랜잭션을 지원하는  쓰기 지연

![Untitled](24_01_10_daily_certification%2032eba37093aa4604b20a423765ca89c5/Untitled%204.png)

![Untitled](24_01_10_daily_certification%2032eba37093aa4604b20a423765ca89c5/Untitled%205.png)

트랜잭션 커밋하는 순간 DB에 쓰기 SQL을 보낸다.

### Entity 수정, 변경 감지 (Dirty Checking)

![Untitled](24_01_10_daily_certification%2032eba37093aa4604b20a423765ca89c5/Untitled%206.png)

```java
// 영속 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

// 영속 엔티티 데이터 수정
memberA.setUsername("hi");
memberA.setAge(10);

// em.persist(memberA); 없는게 맞음, 잘 수정됨
```

### Entity 삭제

```java
// 삭제 대상 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

// 엔티티 삭제
em.remove(memberA);
```

## Flush

영속성 컨텍스트의 변경 내용을 데이터베이스에 반영

- 영속성 컨텍스트를 비우지 않음
- 영속성 컨텍스트의 변경 내용을 데이터베이스에 동기화
- 트랜잭션 작업 단위 → commit 직전에 동기화하면 된다.

### Flush 발생

- 변경 감지 시
- 수정된 엔티니 쓰기 지연 SQL 저장소에 등록
- 쓰기 지연 SQL 저장소의 쿼리를 DB에 전송 (등록, 수정, 삭제 쿼리)

### Persistence Context Flush

- **em.flush()** - 직접 호출
- **트랜잭션 커밋** - Flush 자동 호출
- **JPQL 쿼리 실행** - Flush 자동 호출\

## 준영속 상태

영속 상태의 엔티티가 영속성 컨텍스트에서 분리(detached)되어 영속성 컨텍스트가 제공하는 기능을 사용할 수 없음.

### 준영속 상태로 만드는 법

- em.detach(entity) - 특정 엔티티를 준영속 상태로 전환
- em.clear() - 영속성 컨텍스트를 완전히 초기화
- em.close() - 영속성 컨텍스트를 종료

# Problem Solving (Algorithm & SQL)

**BOJ 16947 서울 지하철 2호선**

[16947번: 서울 지하철 2호선](https://www.acmicpc.net/problem/16947)

N개의 역(정점)이 있고 역과 역 사이를 연결하는 구간(간선)이 N개 존재하므로 반드시 사이클이 1개 생긴다.

사이클에 포함된 정점들을 DFS를 응용하여 구한 후 사이클에 속한 임의의 정점에서 BFS를 하여 다른 정점들까지의 거리를 구한다.

거리를 구할때 정점이 사이클에 속해 있으면 거리를 0으로 하고 사이클에 속하지 않은 정점부터 거리를 1로 시작해서 인접 정점 거리를 계산하면 된다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_16947 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var graph : Array<MutableList<Int>>
    private val isCycle = HashSet<Int>()
    private lateinit var visited : BooleanArray
    private lateinit var finished : BooleanArray
    private lateinit var parents : IntArray
    private var cycleNode = -1
    private lateinit var dist : IntArray

    fun solve() {
        n = br.readLine().toInt()

        graph = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)
        finished = BooleanArray(n + 1)
        parents = IntArray(n + 1)
        dist = IntArray(n + 1)

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            graph[v1].add(v2)
            graph[v2].add(v1)
        }

        findCycle(1, 0)

        Arrays.fill(visited, false)

        bfs()

        for(i in 1 .. n)
            sb.append("${dist[i]} ")
        println(sb)
    }

    private fun bfs() {
        val q = LinkedList<Int>()
        visited[cycleNode] = true
        q.offer(cycleNode)

        while(q.isNotEmpty()) {
            val cur = q.poll()

            for(next in graph[cur]) {
                if(visited[next])
                    continue

                if(isCycle.contains(next))
                    dist[next] = 0
                else
                    dist[next] = dist[cur] + 1

                visited[next] = true
                q.offer(next)
            }
        }
    }

    private fun findCycle(cur : Int, parent : Int) {
        visited[cur] = true

        for(next in graph[cur]) {
            if(!visited[next]) {
                parents[next] = cur
                findCycle(next, cur)
            } else if(!finished[next] && next != parent)
                checkCycle(cur, next)
        }

        finished[cur] = true
    }

    private fun checkCycle(cur : Int, root : Int) {
        isCycle.add(cur)
        if(cycleNode == -1)
            cycleNode = cur

        if(cur == root)
            return

        checkCycle(parents[cur], root)
    }
}

fun main() {
    BOJ_16947().solve()
}
```