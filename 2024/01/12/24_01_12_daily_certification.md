# 24_01_12_daily_certification

```
[koreii] #12 데일리인증
20240111
알고리즘
- 정뱡향 그래프, 역방향 그래프로 나누어 탐색하기 + 난이도 기여
```

# Problem Solving (Algorithm & SQL)

**BOJ 17616 등수 찾기**

[17616번: 등수 찾기](https://www.acmicpc.net/problem/17616)

정방향 그래프와 역방향 그래프를 각각 만들어 x보다 등수가 확실히 높은 학생, x보다 등수가 확실히 낮은 학생을 DFS 또는 BFS로 찾아낸다.

가능한 최고 등수 = 1 + (x보다 확실히 등수가 높은 학생 수)

가능한 최저 등수 = n - (x보다 확실히 등수가 낮은 학생 수)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_17616 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var m = 0
    private var x = 0

    private lateinit var graph1 : Array<MutableList<Int>> //    graph1[i] : i보다 높은 등수인 정점 리스트
    private lateinit var graph2 : Array<MutableList<Int>> //    graph2[i] : i보다 낮은 등수인 정점 리스트
    private lateinit var visited :BooleanArray

    private var u = 0   //  가능한 가장 높은 등수 = 1 + 더 잘한 정점 개수
    private var v = 0   //  가능한 가장 낮은 등수 = n - 더 못한 정점 개수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        x = tokens.nextToken().toInt() - 1

        graph1 = Array(n) { mutableListOf() }
        graph2 = Array(n) { mutableListOf() }
        visited = BooleanArray(n)

        u = 1
        v = n

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt() - 1
            val v2 = tokens.nextToken().toInt() - 1

            graph1[v2].add(v1)  //  v2보다 v1이 더 잘함
            graph2[v1].add(v2)  //  v1보다 v2가 더 못함
        }

        u += (dfs(x, graph1) - 1) //  x보다 더 잘한 정점 개수 추가
        Arrays.fill(visited, false)
        v -= (dfs(x, graph2) - 1) //  x보다 더 못한 정점 개수 빼기

        println("$u $v")
    }

    private fun dfs(v : Int, graph : Array<MutableList<Int>>) : Int {
        var ret = 1

        visited[v] = true

        for(nv in graph[v]) {
            if(!visited[nv])
                ret += dfs(nv, graph)
        }

        return ret
    }
}

fun main() {
    BOJ_17616().solve()
}
```