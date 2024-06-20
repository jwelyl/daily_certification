# 24_06_20_daily_certification

# Side Project

# Git Hook Checkstyle 적용

[[Git Hook] Git Hook 사용하여  Java 코딩 컨벤션 지키기](https://velog.io/@qotndus43/Git-Hook-Git-Hook-사용하여-Java-코딩-컨벤션-지키기)

```
hint: The '.git/hooks/pre-commit' hook was ignored because it's not set as executable.
hint: You can disable this warning with `git config advice.ignoredHook false`.
```

[githook 실행 오류 (not set as executable)](https://den-shin.tistory.com/77)

.git/hooks/pre-commit에 실행 권한을 부여하지 않아서 생기는 에러

# Problem Solving (Algorithm & SQL)

### BOJ 20183 골목 대장 호석 - 효율성 2

[](https://www.acmicpc.net/problem/20183)

```kotlin
import java.io.BufferedReader
import java.io.InputStreamReader
import java.util.*
import kotlin.collections.ArrayList

const val MAX = 1_000_000_000
const val NONE = Long.MAX_VALUE

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0       //  정점 개수
    private var m = 0       //  간선 개수
    private var a = 0       //  시작 정점
    private var b = 0       //  도착 정점
    private var c = 0L      //  이동할 수 있는 최대 거리

    private lateinit var graph : Array<ArrayList<Node>>
    private lateinit var dist : LongArray

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        a = tokens.nextToken().toInt()
        b = tokens.nextToken().toInt()
        c = tokens.nextToken().toLong()

        graph = Array(n + 1) { ArrayList() }
        dist = LongArray(n + 1)

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toInt()

            graph[v1].add(Node(v2, cost.toLong()))
            graph[v2].add(Node(v1, cost.toLong()))
        }

        println(parametricSearch())
    }

    private fun parametricSearch() : Int {
        var start = 1
        var end = MAX
        var res = Int.MAX_VALUE //  a부터 b까지 가면서 만나는 골목들을 통과할 때 각 골목의 최대 길이의 최소

        while(start <= end) {
            val mid = (start + end) / 2 //  a부터 b까지 갈 때 mid보다 긴 길이의 골목은 통과할 수 없음
            val dist = dijkstra(mid)

            if(dist == NONE || dist > c)  //  mid 이하 골목길만으로는 아예 b에 도달할 수 없거나, 도달해도 c보다 더 걸릴 경우
                start = mid + 1 //  더 길이가 긴 골목도 통과 가능하게 해야 함
            else {  //  mid 이하 골목길만으로도 b까지 도달 가능할 경우
                res = mid
                end = mid - 1   //  더 짧은 골목길만으로 b까지 도달 가능한지 체크해야 함
            }
        }

        return if(res == Int.MAX_VALUE) -1 else res
    }

    private fun dijkstra(maxLen : Int) : Long {
        val pq = PriorityQueue<Node>()
        Arrays.fill(dist, NONE)
        dist[a] = 0

        pq.offer(Node(a, dist[a]))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val curV = cur.vertex   //  현재 정점
            val curC = cur.cost     //  curV까지 도달하는 최단 거리

            if(curC > dist[curV])
                continue

            for(next in graph[curV]) {
                val nextV = next.vertex //  다음 정점

                if(next.cost > maxLen)  //  curV와 nextV를 연결하는 골목길의 길이가 maxLen보다 길 경우 해당 골목길은 지나갈 수 없음
                    continue

                val nextC = curC + next.cost

                if(dist[nextV] > nextC) {   //  nextV까지의 최단거리가 갱신될 경우
                    dist[nextV] = nextC
                    pq.offer(Node(nextV, dist[nextV]))
                }
            }
        }

        return dist[b]
    }

    private class Node(val vertex : Int, val cost : Long) : Comparable<Node> {
        override fun compareTo(other: Node): Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    Main().solve()
}
```