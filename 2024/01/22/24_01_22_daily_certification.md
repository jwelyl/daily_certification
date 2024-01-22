# 24_01_22_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 14615 Defend the CTP!!**

[14615번: Defend the CTP!!!](https://www.acmicpc.net/problem/14615)

단방향 그래프에서 1번 정점에서 C번 정점으로 갈 수 있는지, C번 정점에서 N번 정점으로 갈 수 있는지를 파악해야 하는 문제이다. C가 1개만 주어진다면 DFS(또는 BFS) 두 번으로 충분하지만 C가 최대 100,000번 주어지므로 매번 DFS로 파악한다면 200,000번의 DFS를 해야하므로 시간 초과가 발생하게 된다.

1번 정점에서 임의의 C 정점에 도달 가능한지는 한 번의 DFS로 파악 가능하다. 1번 정점에서 DFS를 시작해, 다른 정점에 도달하면 true로 표시하여 다른 임의의 정점에 도달 가능한지 파악할 수 있다.

임의의 정점 C에서 정점 N에 도달 가능한지도 매번 C를 시작 정점으로 DFS한다면 당연히 시간 초과를 피할 수 없다. 하지만 주어진 그래프의 역방향 그래프를 만든다면, 정점 N을 시작으로 임의의 정점 C에 도달 가능한지를 파악하여 해결할 수 있다. 따라서 이 또한 한 번의 DFS로 가능하다.

총 2번의 DFS로 문제를 해결할 수 있다. 최단 거리를 구하는게 아니라 각 정점에 도달 가능한지만 파악하면 되므로, 각 DFS에서 어떤 정점에 도달 가능하면 해당 정점을 다시 방문하지 않도록 주의해야 한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_14615 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var m = 0
    private var t = 0

    private lateinit var graph : Array<Array<MutableList<Int>>>
    private lateinit var visited : Array<BooleanArray>

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        graph = Array(2) { Array(n + 1) { mutableListOf() } }
        visited = Array(2) { BooleanArray(n + 1) }

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            graph[0][v1].add(v2)
            graph[1][v2].add(v1)
        }

        dfs(1, 0, 0)    //  정방향 그래프의 1번 정점에서 모든 정점까지 도달할 수 있나 체크
        dfs(n, 0, 1)       //  역방향 그래프의 n번 정점에서 모든 정점까지 도달할 수 있나 체크(정방향 그래프에서 어떤 정점에서 n번 정점으로 도달할 수 있나 체크)

        t = br.readLine().toInt()

        while(t-- > 0) {
            val mid = br.readLine().toInt()

            sb.append("${if(visited[0][mid] && visited[1][mid]) "Defend the CTP" else "Destroyed the CTP"}\n")
        }

        print(sb)
    }

    private fun dfs(v : Int, d : Int, dir : Int) {
        visited[dir][v] = true

        for(nv in graph[dir][v]) {
            if(!visited[dir][nv])
                dfs(nv, d + 1, dir)
        }
    }
}

fun main() {
    BOJ_14615().solve()
}
```