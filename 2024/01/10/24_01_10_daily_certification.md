# 24_01_10_daily_certification

```
[koreii] #10 데일리인증
20240110

알고리즘
- DFS로 사이클에 속한 정점 찾기 + BFS로 각 정점에서 사이클까지 거리 찾기 + 난이도 기여 
```

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