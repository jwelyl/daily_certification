# 24_01_21_daily_certification

```
[koreii] #21 데일리인증
20240121
알고리즘
- 사이클 판별 & 최소비용신장트리 + 난이도 기여
```

# Problem Solving (Algorithm & SQL)

**BOJ 24977 Visits**

[24977번: Visits](https://www.acmicpc.net/problem/24977)

정점이 N개, 간선이 N개이므로 반드시 사이클이 1개만 발생하게 된다. 사이클에 속한 간선 중 하나를 제거해서 트리를 만들고 트리의 간선의 가중치 합을 최대로 하면 된다.

DFS로 사이클에 속한 간선을 찾아서 그 중 가중치가 가장 작은 간선을 제거해도 되고, Kruskal Algorithm을 응용하여 최대 비용 신장 트리를 구해도 된다.

**코드**

```java
import java.util.*
import java.io.*

class BOJ_24977 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var ds : IntArray
    private val edgeList = ArrayList<Edge>()

    fun solve() {
        n = br.readLine().toInt()

        ds = IntArray(n + 1) { it }

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            val v = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toLong()

            edgeList.add(Edge(it + 1, v, cost))
        }

        println(kruskal())
    }

    private fun kruskal() : Long {
        var mstCost = 0L
        var edgeCnt = 0
        edgeList.sort()

        for(edge in edgeList) {
            val v1 = edge.v1
            val v2 = edge.v2
            val cost = edge.cost

            if(findDS(v1) != findDS(v2)) {
                unionDS(v1, v2)
                mstCost += cost
                edgeCnt++
            }

            if(edgeCnt == n - 1)
                break
        }

        return mstCost
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

    private class Edge(val v1 : Int, val v2 : Int, val cost : Long) : Comparable<Edge> {
        override fun compareTo(edge : Edge) : Int {
            return edge.cost.compareTo(this.cost)
        }
    }
}

fun main() {
    BOJ_24977().solve()
}
```