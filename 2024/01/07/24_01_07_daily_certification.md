# 24_01_07_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 12896 스크루지 민호**

[12896번: 스크루지 민호](https://www.acmicpc.net/problem/12896)

트리 + 깊이 우선탐색 + **트리의 지름**

임의의 정점에서 다른 정점까지의 거리의 최댓값이 최소가 되게 해야 하낟.

트리에서 두 정점의 최대 거리는 트리의 지름으로 임의의 정점에서 dfs로 가장 먼 정점을 구하고, 해당 정점에서 dfs로 가장 먼 정점까지의 거리를 구하면 그것이 트리의 지름이다.

트리의 지름 상에 포함된 정점 중 중앙의 정점에서 지름 양 끝의 정점까지의 거리가 최대 거리의 최소가 된다.(트리의 반지름)

트리에서 간선의 길이가 1이므로 중앙 정점에서 양 끝 정점까지의 거리는 (지름 + 1) / 2가 된다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_12896 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var tree : Array<MutableList<Int>>
    private lateinit var visited : BooleanArray

    private var root = 1
    private var maxDist = 0
    private var maxNode = 0

    fun solve() {
        n = br.readLine().toInt()

        tree = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            tree[v1].add(v2)
            tree[v2].add(v1)
        }

        dfs(root, 0)

        root = maxNode
        maxDist = 0
        Arrays.fill(visited, false)

        dfs(root, 0)

        println((maxDist + 1) / 2)
    }

    private fun dfs(v : Int, dist : Int) {
        visited[v] = true

        if(dist > maxDist) {
            maxDist = dist
            maxNode = v
        }

        for(nv in tree[v]) {
            if(!visited[nv])
                dfs(nv, dist + 1)
        }
    }
}

fun main() {
    BOJ_12896().solve()
}
```