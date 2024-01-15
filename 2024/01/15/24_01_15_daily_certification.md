# 24_01_15_daily_certification

```
[koreii] #15 데일리인증
20240115
JPA
- Entity Mapping 학습, EnumType으로 String 써야 하는 이유
- DB Schema 자동 생성 학습
-
알고리즘
- Tree DFS/BFS + Greedy 문제 해결 + 난이도 기여
```

# JPA

## Entity Mapping

@Entiy, @Table, @Column Annotation

Mapping Annotation

**@Enumerated (EnumType.STRING 사용)**

## DB Schema 자동 생성

- DDL 자동 생성
- DB에 맞는 적절한 DDL 생성
- 개발 장비에서만 사용해야 함
- 운영서버에서는 직접 사용하지 않고 DDL을 적절히 다듬어서 사용

### hibernate.hbm2ddl.auto

- create
- create-drop
- update
- validate
- none

## 기본키 매핑

# Problem Solving (Algorithm & SQL)

**BOJ 27924 윤이는 엄청난 것을 훔쳤습니다.**

[27924번: 윤이는 엄청난 것을 훔쳐갔습니다](https://www.acmicpc.net/problem/27924)

세 개의 출발 정점에 대해 시뮬레이션 형식으로 모든 경우를 따져보기에는 정점 개수가 너무 많다.

세 출발 정점을 각각 A, B, C라고 할 때 A, B, C로부터 모든 리프노드까지의 거리를 구하고 (distA, distB, distC), distA < distB && distA < distC인 리프 노드가 하나라도 존재하면 탈출할 수 있다.

트리에서는 어떤 두 정점 사이의 경로가 유일하고, 또한 리프 노드에 도달하기 위해서는 결국 하나의 경로를 공유하게 되어있다. 따라서 더 빨리 도달할 수 있으면 따라잡히거나 마주치지 않고 도달할 수 있다.

트리의 성질을 잘 파악해서 그리디한 접근도 해야하는 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_27924 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var tree : Array<MutableList<Int>>
    private lateinit var cnt : IntArray //  cnt[i] : 정점 i와 인접한 정점 개수, 1일 경우, i는 리프 노드
    private lateinit var visited : BooleanArray
    private val pos = IntArray(3)   //  pos[i] : i의 시작 위치 i = 0(윤이), 1(달구), 2(포닉스)
    private lateinit var dist : Array<IntArray> //  dist[i][j] : i가 j에 도달하기 위한 거리 i = 0(윤이), 1(달구), 2(포닉스)

    fun solve() {
        n = br.readLine().toInt()

        tree = Array(n + 1) { mutableListOf() }
        cnt = IntArray(n + 1)
        visited = BooleanArray(n + 1)
        dist = Array(3) { IntArray(n + 1) }

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            tree[v1].add(v2)
            tree[v2].add(v1)

            cnt[v1]++
            cnt[v2]++
        }

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< 3)
            pos[i] = tokens.nextToken().toInt()

        for(i in 0 ..< 3) {
            Arrays.fill(visited, false)
            dfs(pos[i], dist[i], 0)
        }

        for(i in 1 .. n) {
            if(cnt[i] == 1 && dist[0][i] < dist[1][i] && dist[0][i] < dist[2][i]) {
                println("YES")
                return
            }
        }

        println("NO")
    }

    private fun dfs(pos : Int, dist : IntArray, d : Int) {
        visited[pos] = true
        dist[pos] = d

        for(npos in tree[pos]) {
            if(!visited[npos])
                dfs(npos, dist, d + 1)
        }
    }
}

fun main() {
    BOJ_27924().solve()
}
```