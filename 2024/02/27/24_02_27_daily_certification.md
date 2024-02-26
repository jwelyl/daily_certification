# 24_02_27_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 17940 일감호에 다리 놓기**

[17490번: 일감호에 다리 놓기](https://www.acmicpc.net/problem/17490)

일단 공사중인(편의상 부서진) 구간 개수 M이 1 이하면 와우도(N + 1)과 연결하지 않고도 강의동 1~N은 연결되어 있다. 따라서 K의 개수에 관계 없이 항상 YES이다.

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled.png)

M = 0인 경우 이미 연결되어 있다.

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled%201.png)

M = 1인 경우 N개의 구간 중 어느 하나가 부서졌다 쳐도 나머지 N-1개의 구간으로 연결되어 있다.

따라서 M이 2 이상인 경우에만 생각하면 된다.

예제 1, 2번의 상황은 다음 그림과 같다.

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled%202.png)

부서진 구간은 점선으로 표시했고 멀쩡한 구간은 빨간색 실선으로 표시했다. 와우도와 연결하는 선은 파란색으로 표시했다. 간선의 숫자는 필요한 돌 개수이다. 멀쩡한 구간에는 돌이 필요없다.

2개 이상의 구간이 부서진 이상 1~N 사이 간선만으로는 1~N을 전부 연결할 수 없다. 따라서 반드시 N+1을 통해 연결되어야 한다. 즉, N+1개의 정점을 연결하는 최소 비용 신장 트리를 구축해야 한다.

간선을 구현하기 위해 Edge 클래스를 구현했다. 

Section 클래스는 간선의 두 정점 정보가 (번호가 작은 정점, 번호가 큰 정점) 형태로 저장되어 있다.

부서진 간선 정보는 broken이라는 HashSet에 간선 구간을 Section으로 저장한다.

2N개의 간선을 모두 만들고, 각각의 간선을 broken에서 검색하여, broken에 포함되어 있으면 사용할 수 없는 간선이므로 include를 false로 처리한다. (아마 이 부분이 시간이 오래 걸리는 것 같다.)

모든 간선의 include를 확정한 후에는 Kruskal Algorithm으로 최소 비용 신장 트리의 cost를 구하면 된다. include가 false인 간선은 우선적으로 제외해야 한다.

어차피 간선들은 크기 순으로 정렬되므로 부서지지 않은 간선들이 우선되게 된다. 해당 간선들을 전부 연결해도 사이클이 발생하지 않으므로 반드시 MST에 포함된다. 따라서 별도 처리를 해주지 않아도 된다. 

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled%203.png)

MST의 cost를 구한 후 K와 비교해서 K보다 작거나 같으면 YES를, 아니면 NO를 출력한다.

### 코드 (성공했지만 많이 느림)

```kotlin
import java.util.*
import java.io.*

class BOJ_17490 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  강의동 개수 (1 ~ n : 강의동 번호, n + 1 : 와우도)
    private var m = 0   //  공사 구간 개수
    private var k = 0L   //  돌 개수

    private val brokens = HashSet<Section>()    //  부서진 구간 저장

    private lateinit var ds : IntArray  //  ds[i] : i가 속한 분리집합의 우두머리

    private val edgeList = ArrayList<Edge>()    //  간선 저장 리스트

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        k = tokens.nextToken().toLong()

        if(m <= 1)  //  부서진 구간이 1개 이하면 이미 강의동끼리 연결되어 있음
            println("YES")
        else {  //  부서진 구간이 2개 이상이면 반드시 와우도랑 연결해야 함
            tokens = StringTokenizer(br.readLine())

            //  각 강의동과 와우도까지 연결 비용
            for(i in 1 .. n)
                edgeList.add(Edge(Section(i, n + 1), tokens.nextToken().toInt(), true))

            //  각 강의동끼리 연결
            for(i in 1..< n)
                edgeList.add(Edge(Section(i, i + 1), 0, true))
            edgeList.add(Edge(Section(1, n), 0, true))

            //  부서진 구간 정보 받기
            repeat(m) {
                tokens = StringTokenizer(br.readLine())
                val v1 = tokens.nextToken().toInt()
                val v2 = tokens.nextToken().toInt()

                brokens.add(Section(v1.coerceAtMost(v2), v1.coerceAtLeast(v2)))
            }

            var brokenCnt = 0  // 부서진 간선 찾은 개수

            for(edge in edgeList) {
                val section = edge.section

                if (brokens.contains(section)) {  //  현재 간선이 부서진 구간이면
                    edge.include = false    //  간선이 포함될 수 없도록 표시한다.
                    brokenCnt++
                }

                if(brokenCnt == m)  //  부서진 간선을 모두 찾았을 경우
                    break
            }

            ds = IntArray(n + 2) { it }

            println(if(kruskal() > k) "NO" else "YES")
        }
    }

    private fun kruskal() : Long {
        var mstCost = 0L
        var mstEdgeCnt = 0

        edgeList.sort()

        for(edge in edgeList) {
            if(!edge.include)   //  부서진 구간 간선이면
                continue

            val v1 = edge.section.v1
            val v2 = edge.section.v2
            val v1DS = findDS(v1)
            val v2DS = findDS(v2)

            if(v1DS != v2DS) {
                unionDS(v1DS, v2DS)
                mstEdgeCnt++
                mstCost += edge.cost
            }

            if(mstEdgeCnt == n)
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

    private data class Section(val v1 : Int, val v2 : Int)

    private class Edge(val section: Section, val cost : Int, var include : Boolean) : Comparable<Edge> {
        override fun compareTo(other : Edge) : Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    BOJ_17490().solve()
}
```

간선을 구축하고 부서진 간선을 구별하는 더 좋은 방법을 생각해봐야겠다.