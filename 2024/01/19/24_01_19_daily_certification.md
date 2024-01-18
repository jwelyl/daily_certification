# 24_01_19_daily_certification

```
[koreii] #19 데일리인증
20240119
알고리즘
- 트리 지름 찾기, 지름이 여러 개일 경우, 이동 시간이 가장 작은 지름 찾기 + 난이도 기여
```

# Problem Solving (Algorithm & SQL)

**BOJ 14657 준오는 최종인재야!!**

[14657번: 준오는 최종인재야!!](https://www.acmicpc.net/problem/14657)

트리의 각 간선의 길이를 1로 봤을 때 임의의 두 정점 사이의 거리가 최대인 것은 두 정점 사이 경로에 속한 정점의 개수가 가장 많은 것으로 생각할 수 있다. 모든 간선의 길이를 1로 간주하여 트리의 지름을 구하고 그 지름의 실제 길이(주어진 간선 길이를 이용)를 구하면 된다.

트리의 지름이 여러 개일 수 있다. 지름이 여러 개일 경우, 지름의 실제 길이가 가장 작은 지름을 구하면 된다.

일반적인 트리의 지름 구하기 문제처럼 루트를 기준으로 간선의 길이를 1로 생각했을때 최대 간선이 되는 정점을 DFS로 찾고, 그 정점을 다시 루트로 해서 DFS로 다시 지름을 찾으면 된다. 다만 간선 길이를 1로 가정하고 구한 지름 개수가 여러 개일 경우, 실제 간선 길이를 고려해서 실제 지름 길이가 가장 짧은 것을 구하면 된다.

간선 1로 했을 때 길이와 실제 간선 길이를 모두 고려해야 함에도 기존 트리의 지름 구하기 방법과 같은 풀이가 가능한 이유는 정확히 모르겠다. 여러 지름이 존재할 경우에도 성립하는 이유를 확인해야 한다.

**코드**

```java
import java.util.*
import java.io.*

class BOJ_14657 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var t = 0

    private lateinit var tree : Array<MutableList<Node>>
    private lateinit var visited : BooleanArray
    private var root = 1
    private var diameter = 0
    private var farthest = 0
    private var minTime = Int.MAX_VALUE

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        t = tokens.nextToken().toInt()

        tree = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toInt()

            tree[v1].add(Node(v2, cost))
            tree[v2].add(Node(v1, cost))
        }

        dfs(root, 0, 0)
        root = farthest
        diameter = 0
        minTime = Int.MAX_VALUE
        Arrays.fill(visited, false)
        dfs(root, 0, 0)

        println(if(minTime % t == 0) minTime / t else minTime / t + 1)
    }

    private fun dfs(v : Int, dist : Int, time : Int) {
        visited[v] = true

        if(dist > diameter) {
            diameter = dist
            farthest = v
            minTime = time
        } else if(dist == diameter && minTime > time) {
            farthest = v
            minTime = time
        }

        for(node in tree[v]) {
            val nv = node.v
            val ndist = dist + 1
            val ntime = time + node.time

            if(!visited[nv])
                dfs(nv, ndist, ntime)
        }
    }

    private class Node(val v : Int, val time : Int)
}

fun main() {
    BOJ_14657().solve()
}
```