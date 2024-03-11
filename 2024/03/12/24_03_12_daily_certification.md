# 24_03_12_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 20188 등산 마니아**

[20188번: 등산 마니아](https://www.acmicpc.net/problem/20188)

v1 < v2인 임의의 두 정점 v1, v2를 골랐을 때 v1에서 v2로 가는 방법의 경우의 수는 v1에서 v2로 가는데 지나게 되는 간선의 개수이다. 어차피 루트인 1번을 거쳐서 v1에서 v2로 가야하고, 같은 간선을 여러번 지나도 1번 지난 것으로 치므로 v1, v2의 대소관계는 사실 중요하지 않다. (2, 1)을 뽑나 (1, 2)를 뽑나 같은 경우이므로 (1, 2)를 뽑는 경우만 고려하면 된다.

v1 < v2인 모든 가능한 서로 다른 (v1, v2) 쌍에서 v1에서 v2로 가는데 지나는 간선의 개수를 모두 더하면 모든 간선이 지나지는 경우의 수의 합과 같다. 즉 n-1개의 간선에 대하여 각 간선이 지나지는 경우의 수를 구하고 합하면 된다.

**예제 2**

![ex2.jpeg](24_03_12_daily_certification%20bb7ee25c427f4402aad4d96d84917bd4/ex2.jpeg)

예제 2의 트리이다. 간선 (5, 6)이 지나지는 횟수를 계산해보자.

![ex2_1.jpeg](24_03_12_daily_certification%20bb7ee25c427f4402aad4d96d84917bd4/ex2_1.jpeg)

정정이 n개인 트리에서 임의의 점 2개를 뽑는 경우의 수는 nC2인데 이는 다음 세 가지 경우로 나눌 수 있다.

1. 위의 그림 A에 포함된 정점 1개, B에 포함된 정점 1개
2. 위의 그림 B에 포함된 정점 2개
3. 위의 그림 A에 포함된 정점 2개

1, 2에서 2개의 정점 v1, v2를 뽑을 경우 v1에서 v2로 가는 경로에는 반드시 간선 (5, 6)이 포함된다. 2의 경우에도 루트 정점 1을 지나서 가야 하므로 반드시 간선 (5, 6)을 지나야 한다.

하지만 3의 경우 어떻게 뽑아도 (5, 6)을 지나지 않는다.

따라서 간선 (5, 6)을 지나도록 두 정점을 뽑는 경우의 수는 nC2 - (A에서 2개 정점을 뽑는 경우의 수)이다.

**A에 속한 정점의 개수를 nA라고 하고 B에 속한 정점 개수를 nB라고 하면 nA + nB = n이고,**

**간선 (5, 6)을 지나는 경우의 수 = nC2 - nAC2 이다.**

![ex2_2.jpeg](24_03_12_daily_certification%20bb7ee25c427f4402aad4d96d84917bd4/ex2_2.jpeg)

이 때, nB는 6을 root로 하는 subtree에 포함된 정점 개수이다.

각 정점을 root로 하는 subtree의 정점 수는 DFS로 O(n)에 구할 수 있다.

![ex2_3.jpeg](24_03_12_daily_certification%20bb7ee25c427f4402aad4d96d84917bd4/ex2_3.jpeg)

정점 6을 root로 하는 subtree의 정점 개수는 3개이므로 nB = 3이고 nA = 5이다.

따라서 간선 (5, 6)을 지나는 경우의 수는 8C2 - 5C2 = 18이다.

주의 사항으로 임의의 간선 (v1, v2)를 구할 때 nA, nB를 나누기 위해 subtree를 구할 정점을 잘 정해야 한다.

![ex2_4.jpeg](24_03_12_daily_certification%20bb7ee25c427f4402aad4d96d84917bd4/ex2_4.jpeg)

(v1, v2) 중 더 깊이가 깊은 정점을 v라 하고 v를 루트로 하는 subtree를 구해야 한다. 그렇지 않으면 subtree에 간선 (v1, v2)가 포함되게되어 올바른 정답을 구할 수 없다.

각 정점의 깊이는 subtree에 속한 정점 개수를 구할 때 DFS로 함께 구할 수 있다.

**코드**

```kotlin
import java.util.*
import java.io.*

const val ROOT = 1    // 전체 트리 root는 1

class BOJ_20188 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    정점 개수
    private lateinit var tree : Array<ArrayList<Int>>    //    트리

    private lateinit var subtree : IntArray    //    subtree[i] : i번 노드를 root로 하는 subtree의 정점 개수
    private lateinit var depth : IntArray      //    depth[i] : ROOT의 깊이를 1로 했을 때 i번 노드의 깊이 (0일 경우 미방문)
    private lateinit var edges : Array<IntArray>    //    edges[i] : i번째 간선 [v1, v2]

    private var ans = 0L    //    경우의 수

    fun solve() {
        n = br.readLine().toInt()

        tree = Array(n + 1) { ArrayList() }
        subtree = IntArray(n + 1)
        depth = IntArray(n + 1)
        edges = Array(n - 1) { intArrayOf(0, 0) }

        repeat(n - 1) {    //    v1, v2를 잇는 n-1개의 간선 정보 받기
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            edges[it][0] = v1
            edges[it][1] = v2

            tree[v1].add(v2)
            tree[v2].add(v1)
        }

        dfs(ROOT, 1)    //    루트 깊이를 1로 dfs

        repeat(n - 1) {    //    모든 간선에 대하여
            val v1 = edges[it][0]
            val v2 = edges[it][1]

            val v = if(depth[v1] < depth[v2]) v2 else v1    //    깊이가 더 깊은 정점

            val na = n - subtree[v]

            ans += (n.toLong() * (n - 1)) / 2 - (na.toLong() * (na - 1)) / 2
        }

        println(ans)
    }

    private fun dfs(v : Int, d : Int) : Int {
        subtree[v] = 1    //    v를 포함하므로 1개 확보
        depth[v] = d      //    v의 깊이는 d + 1

        for(nv in tree[v]) {    //    v의 주변 정점들 중에서
            if(depth[nv] == 0)    //    아직 방문 안한 정점일 경우, 즉 v의 자식 정점일 경우
                subtree[v] += dfs(nv, d + 1)    //    깊이가 d + 1인 nv를 root로 하는 subtree의 정점 개수를 더해줌
        }

        return subtree[v]
    }
}

fun main() {
    BOJ_20188().solve()
}
```