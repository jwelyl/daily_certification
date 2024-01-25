# 24_01_25_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1135 뉴스 전하기**

[1135번: 뉴스 전하기](https://www.acmicpc.net/problem/1135)

### 1트 → (정점 개수가 많은 서브트리부터 전달하기)

일단 DFS로 각 정점을 루트로 하는 서브트리의 정점 개수를 subtrees에 정점 별로 저장했다. 그리고 0번 정점에서 시작해서 자식 정점 중 subtrees 값이 큰 정점부터 탐색해서 전달하기로 구현했다.

![1.jpeg](24_01_25_daily_certification%206b22281af04b4ec18ac53b0bbde3593a/1.jpeg)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1135 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  직원 수 N
    private lateinit var tree : Array<MutableList<Int>> //  트리
    private lateinit var queueTree : Array<PriorityQueue<Int>>  //  트리
    private lateinit var subtrees : IntArray    //  subtrees[i] : i를 루트로 하는 서브트리에 포함된 정점 개수
    private lateinit var visited : BooleanArray

    fun solve() {
        n = br.readLine().toInt()

        tree = Array(n) { mutableListOf() }
        queueTree = Array(n) { PriorityQueue { it1, it2 -> subtrees[it2] - subtrees[it1] } }
        subtrees = IntArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n) {
            val parent = tokens.nextToken().toInt()

            if(parent != -1)
                tree[parent].add(i)
        }

        dfs(0)

//        println(subtrees.contentToString())

        for(i in 0 ..< n) {
            for(v in tree[i])
                queueTree[i].add(v)
//            println("$i = ${queueTree[i]}")
        }

        propagate()
    }

    private fun propagate() {
        var time = 0
        val q = LinkedList<Int>()

        if(queueTree[0].isNotEmpty())
            q.offer(0)

        while(q.isNotEmpty()) {
//            println("queue = $q")
            val size = q.size

            for(s in 0 ..< size) {
                val now = q.poll()
                val next = queueTree[now].poll()

                if(next != null)
                    q.offer(next)
                if(queueTree[now].isNotEmpty())
                    q.offer(now)
            }

            time++
        }

        println(time - 1)
    }

    private fun dfs(v : Int) : Int {
        subtrees[v] = 1 //  일단 자기 자신 1개

        for(nv in tree[v])
            subtrees[v] += dfs(nv)  //  자식 노드를 루트로 하는 서브트리 정점 개수 더하기

        return subtrees[v]
    }
}

fun main() {
    BOJ_1135().solve()
}
```

**결과**

![Untitled](24_01_25_daily_certification%206b22281af04b4ec18ac53b0bbde3593a/Untitled.png)

5%에서 처참하게 틀렸다.

### 2트 → (깊이가 깊은 서브트리부터 탐색, 깊이가 같을 경우 정점이 많은 서브트리부터 탐색)

최초 DFS 과정에서 추가로 각 정점의 깊이를 계산했다. 리프 노드의 깊이를 0으로 계산해서 각 정점의 깊이를, 자식 정점의 깊이 중 최댓값 + 1로 결정하였다. 뉴스 전달 순서를 깊이가 깊은 서브트리부터 탐색하고, 깊이가 같을 경우 정점이 많은 서브트리부터 탐색하도록 수정했다.

![2.jpeg](24_01_25_daily_certification%206b22281af04b4ec18ac53b0bbde3593a/2.jpeg)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1135 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  직원 수 N
    private lateinit var tree : Array<MutableList<Int>> //  트리
    private lateinit var queueTree : Array<PriorityQueue<Int>>  //  트리
    private lateinit var subtrees : IntArray    //  subtrees[i] : i를 루트로 하는 서브트리에 포함된 정점 개수
    private lateinit var depth : IntArray       //  depth[i] : i를 루트로 하는 서브트리의 깊이

    fun solve() {
        n = br.readLine().toInt()

        tree = Array(n) { mutableListOf() }
        queueTree = Array(n) { PriorityQueue { it1, it2 -> if(depth[it2] - depth[it1] != 0) depth[it2] - depth[it1] else subtrees[it2] - subtrees[it1] } }
        subtrees = IntArray(n)
        depth = IntArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n) {
            val parent = tokens.nextToken().toInt()

            if(parent != -1)
                tree[parent].add(i)
        }

        dfs(0)

//        println(subtrees.contentToString())
//        println(depth.contentToString())

        for(i in 0 ..< n) {
            for(v in tree[i])
                queueTree[i].add(v)
//            println("$i = ${queueTree[i]}")
        }

        propagate()
    }

    private fun propagate() {
        var time = 0
        val q = LinkedList<Int>()

        if(queueTree[0].isNotEmpty())
            q.offer(0)

        while(q.isNotEmpty()) {
//            println("queue = $q")
            val size = q.size

            for(s in 0 ..< size) {
                val now = q.poll()
                val next = queueTree[now].poll()

                if(next != null)
                    q.offer(next)
                if(queueTree[now].isNotEmpty())
                    q.offer(now)
            }

            time++
        }

        println(time - 1)
    }

    private fun dfs(v : Int) : Int {
        subtrees[v] = 1 //  일단 자기 자신 1개

        for(nv in tree[v]) {
            subtrees[v] += dfs(nv)  //  자식 노드를 루트로 하는 서브트리 정점 개수 더하기
            depth[v] = depth[v].coerceAtLeast(depth[nv] + 1)
        }

        return subtrees[v]
    }
}

fun main() {
    BOJ_1135().solve()
}
```

**결과**

![Untitled](24_01_25_daily_certification%206b22281af04b4ec18ac53b0bbde3593a/Untitled%201.png)

6%에서 처참하게 틀렸다.

### 3트 → 자식 노드를 정점으로 하는 서브트리 중 탐색이 가장 오래 걸리는 서브트리부터 탐색

사실 깊이가 깊은 서브트리 우선, 정점 수가 많은 서브트리 우선 탐색 모두 반례가 존재하는, 최적의 방법이 아니다. 말 그대로 문제가 요구하는 대로 가장 탐색이 오래 걸리는 자식 서브트리부터 빨리 탐색을 시작하는 것이다. (Greedy), 정점 v의 자식 정점을 v1, v2, v3, …, vc라고 하자. 각 서브트리를 모두 탐색하는데 걸리는 시간 time이 time[v1] ≥ time[v2] ≥ … ≥ time[vc]라 할 때 time[v] = max(time[v1] + 1, time[v2] + 2, .. time[vc] + c)이다. (DP)

리프 노드의 time이 0인 것을 이용해서 dfs로 모든 노드의 탐색 시간을 구할 수 있다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_1135 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  직원 수 N
    private lateinit var tree : Array<MutableList<Node>> //  트리

    fun solve() {
        n = br.readLine().toInt()
        tree = Array(n) { mutableListOf() }

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n) {
            val parent = tokens.nextToken().toInt()

            if(parent != -1)
                tree[parent].add(Node(i, 0))
        }

        println(dfs(0))
    }

    //  v를 루트로 하는 서브트리의 모든 노드에 뉴스 전하는 최대 시간
    private fun dfs(v : Int) : Int {
        var max = 0
        var time = 0

        for(child in tree[v])
            child.time = dfs(child.v)

        tree[v].sort()
        for(child in tree[v])
            max = max.coerceAtLeast(child.time + (++time))

        return max
    }

    //  트리 노드 번호, 방문 시간
    private class Node(val v : Int, var time : Int) : Comparable<Node> {
        override fun compareTo(other: Node): Int {
            return other.time.compareTo(this.time)
        }
    }
}

fun main() {
    BOJ_1135().solve()
}
```
