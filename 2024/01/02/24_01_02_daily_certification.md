# 24_01_02_tue_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 20010 악덕 영주 혜유**

[20010번: 악덕 영주 혜유](https://www.acmicpc.net/problem/20010)

**최소 비용 신장 트리 + 깊이 우선 탐색 (트리의 지름)**

Kruskal 알고리즘을 이용해 mst에 포함되는 간선을 찾는다. 이 때, 간선을 graph에 추가하여 트리를 실제로 만든다.

mst를 구한 후 해당 트리의 지름을 구한다. 임의의 정점에서 DFS를 하여 가장 먼 정점을 구하고, 그 정점에서 다시 한번 DFS를 하여 가장 먼 정점을 구한다. 그 때 가장 먼 정점까지의 거리가 트리의 지름이다.

두 번째 DFS 할 때 방문 상태를 초기화해줘야 한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_20010 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var k = 0
    private val edgeList = mutableListOf<Edge>()
    private lateinit var graph : Array<MutableList<Node>>
    private lateinit var ds : IntArray

    private lateinit var visited : BooleanArray
    private var root = 0
    private var farthestDist = 0
    private var farthestVertex = 0

    private var mstCost = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        graph = Array(n) { mutableListOf() }
        ds = IntArray(n) { it }
        visited = BooleanArray(n)

        repeat(k) {
            tokens = StringTokenizer(br.readLine())

            edgeList.add(Edge(tokens.nextToken().toInt(), tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        kruskal()

        println(mstCost)
        println(findDiameter())
    }

    private fun findDS(v : Int) : Int {
        if(v == ds[v])
            return v

        ds[v] = findDS(ds[v])
        return ds[v]
    }

    private fun unionDS(v1 : Int, v2 : Int) {
        val v1DS = findDS(v1)
        val v2DS = findDS(v2)

        if(v1DS != v2DS)
            ds[v2DS] = v1DS
    }

    private fun kruskal() {
        edgeList.sort()
        var edgeCnt = 0

        for(edge in edgeList) {
            val v1DS = findDS(edge.v1)
            val v2DS = findDS(edge.v2)

            if(v1DS != v2DS) {
                unionDS(v1DS, v2DS)
                edgeCnt++
                mstCost += edge.cost

                graph[edge.v1].add(Node(edge.v2, edge.cost))
                graph[edge.v2].add(Node(edge.v1, edge.cost))
            }

            if(edgeCnt == n - 1)
                break
        }
    }

    private fun findDiameter() : Int {
        dfs(root, 0)
        root = farthestVertex
        Arrays.fill(visited, false)
        dfs(root, 0)
        return farthestDist
    }

    private fun dfs(v : Int, dist : Int) {
        visited[v] = true

        if(farthestDist < dist) {
            farthestDist = dist
            farthestVertex = v
        }

        for(edge in graph[v]) {
            if(!visited[edge.v])
                dfs(edge.v, dist + edge.cost)
        }
    }

    private class Edge(val v1 : Int, val v2 : Int, val cost : Int) : Comparable<Edge> {
        override fun compareTo(other: Edge): Int {
            return this.cost.compareTo(other.cost)
        }
    }

    private class Node(val v : Int, val cost : Int)
}

fun main() {
    BOJ_20010().solve()
}
```