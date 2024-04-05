# 24_04_05_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1774 우주신과의 교감**

[1774번: 우주신과의 교감](https://www.acmicpc.net/problem/1774)

두 가지를 주의해야 한다.

이미 연결된 m개의 간선이 주어질 때 간선의 두 정점을 무작정 union하면 안된다. union 로직 안에서 find로 각 정점의 우두머리를 찾고 두 정점의 우두머리가 서로 다를 때에만 실제로 union해야 한다.

이미 연결된 m개의 간선이 주어지지만 이 m개의 간선이 모두 MST에 포함되는 간선이라는 보장이 없다. 따라서 실제로 MST를 만들기 위해 필요한 간선 수는 n - 1 -m개가 아니다. m개의 간선 중 MST에 포하되는 간선 개수를 찾아야 한다.

![Untitled](24_04_05_daily_certification%207aff280eb1d245e986fa97701499226f/Untitled.png)

위의 두 가지를 간과하여 위와 같이 고생했다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.*

class BOJ_1774 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0       //    정점 개수
    private var m = 0       //    이미 연결된 간선 개수
    private var valid = 0   //  실제로 유효하게 미리 연결된 간선 개수

    private val posList = ArrayList<Pos>()            //    좌표 목록
    private val edges = ArrayList<Edge>()         //    간선 목록
    private lateinit var ds : IntArray            //    각 정점이 속한 분리 집합

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        ds = IntArray(n + 1) { it }

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            val y = tokens.nextToken().toDouble()
            val x = tokens.nextToken().toDouble()

            posList.add(Pos(y, x))
        }

        for(i in 0 ..< n - 1) {
            for(j in i + 1 ..< n) {
                val v1 = i + 1
                val v2 = j + 1
                val cost = sqrt((posList[j].y - posList[i].y).pow(2) + (posList[j].x - posList[i].x).pow(2))

                edges.add(Edge(v1, v2, cost))
            }
        }

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            val v1DS = findDS(v1)
            val v2DS = findDS(v2)

            if(v1DS != v2DS) {
                unionDS(v1DS, v2DS)
                valid++
            }
        }

        println(String.format("%.2f", kruskal()))

    }

    private fun findDS(v : Int) : Int {
        if(v == ds[v])
            return v

        ds[v] = findDS(ds[v])
        return ds[v]
    }

    private fun unionDS(v1 : Int, v2 : Int) {
        ds[v2] = v1
    }

    private fun kruskal() : Double {
        var edgeCnt = n - 1 - valid     //    mst가 완성되기 위해 추가로 연결해야 하는 간선 개수
        var mstCost = 0.0               //    mst가 완성되기 위해 추가로 필요한 비용

        edges.sort()

        for(edge in edges) {
            val v1 = edge.v1
            val v2 = edge.v2
            val cost = edge.cost

            val v1DS = findDS(v1)
            val v2DS = findDS(v2)

            if(v1DS != v2DS) {
                unionDS(v1DS, v2DS)
                mstCost += cost
                edgeCnt--
            }
            
            if(edgeCnt == 0)
                break
        }

        return mstCost
    }

    private class Pos(val y : Double, val x : Double) {}

    private class Edge(val v1 : Int, val v2 : Int, val cost : Double) : Comparable<Edge> {
        override fun compareTo(other : Edge) : Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    BOJ_1774().solve()
}
```