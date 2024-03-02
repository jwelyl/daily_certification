# 24_03_02_daily_certification

```
[koreii] #62 데일리인증
20240302
알고리즘
- DFS & DP 문제 풀이
```

# Problem Solving (Algorithm & SQL)

**BOJ 20171 Dessert Café**

[20171번: Dessert Café](https://www.acmicpc.net/problem/20171)

문제가 영어로 되어 있어 이해가 잘 안 됐지만 예제 1과 2를 보면 결국 아파트 단지를 포함하여, 임의의 두 아파트 단지 사이의 정점 개수를 구하는 문제이다.

[ex.avif](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex.avif)

일단 임의의 아파트 단지 정점에서부터 시작해서 dfs로 탐색한다. 주변 정점이 이미 탐색한 정점이면(checke가 0이 아니면) 탐색을 스킵하고 아직 탐색하지 않은 정점이면 탐색한다. 현재 정점이 아파트 단지거나, 주변 정점이 아파트 단지로 이어지면(주변 정점 중 checked가 1인 경우가 존재하면) checked를 1로 해서 반환하고, 현재 정점이 아파트 단지도 아니고, 주변 정점 중 아파트 단지로 이어지는 정점도 없으면 checked를 -1로 해서 반환한다.

주변 정점에 대한 dfs가 모두 끝난 후에야 현재 정점으로 돌아오므로 별 다른 처리가 없으면 두 정점을 반복해서 dfs하게 되므로 일단 dfs로 한 정점을 탐색 시작하면 checked를 2로 설정하여 다시 dfs하는 일이 없도록 하였다.

**예제 1**

![ex1_1.jpeg](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex1_1.jpeg)

아파트 단지인 정점 2에서 시작하여 다른 아파트 단지까지 가는 중간에 거치게 되는 정점들을 모두 포함하면 된다.

![ex1_2.jpeg](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex1_2.jpeg)

정점 1, 3, 7의 경우 아파트 단지인 2에서부터 시작해서 갔을 때 다른 아파트 단지와 이어지지도 않고, 그 자신도 아파트 단지가 아니므로 not ok가 된다. 그 외 정점들은 그 자신이 아파트 단지이거나 정점 2와 아파트 단지 사이의 경로에 존재하므로 ok이다.

![ex3jpeg.jpeg](24_03_02_daily_certification%20847c91f58a84427ab02a0e9c1d27feef/ex3jpeg.jpeg)

다른 아파트 단지를 만났다고 탐색을 종료해서는 안된다. 위와 같은 그래프의 경우 2에서 시작해서 또 다른 아파트 단지 4를 만나고, 4에서부터 또 다른 아파트 단지를 찾아 그 사이에 있는 정점도 추가해줘야 한다.

### 코드

```kotlin
import java.util.*
import java.io.*

class BOJ_20171 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    정점 개수
    private var k = 0    //    아파트 단지 개수

    private lateinit var graph : Array<ArrayList<Int>>    //    그래프
    private lateinit var isComplex : BooleanArray         //    아파트 단지면 true
    private lateinit var checked : IntArray    // 0이면 아직 미방문, 1이면 ok, -1이면 not ok, 2면 대기 중

    private var start = -1     //    dfs 시작할 아파트 단지
    private var ans = 0        //    ok인 지점 개수

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        k = tokens.nextToken().toInt()

        graph = Array(n + 1) { ArrayList() }
        isComplex = BooleanArray(n + 1)
        checked = IntArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()    //    두 정점 사이 간선
            tokens.nextToken()    //    가중치 필요 없음

            graph[v1].add(v2)
            graph[v2].add(v1)
        }

        tokens = StringTokenizer(br.readLine())
        repeat(k) {
            val v = tokens.nextToken().toInt()
            isComplex[v] = true

            if(start == -1)
                start = v
        }

        dfs(start)

        println(ans)
    }

    private fun dfs(v : Int) : Int {
        checked[v] = 2  //  일단 방문했고 대기 중으로 바꾸기

        var isOk = false

        for(nv in graph[v]) {
            if(checked[nv] != 0)
                continue

            if(dfs(nv) == 1)
                isOk = true
        }

        if(isComplex[v] || isOk) {
            ans++
            checked[v] = 1
        }
        else checked[v] = -1

        return checked[v]
    }
}

fun main() {
    BOJ_20171().solve()
}

```