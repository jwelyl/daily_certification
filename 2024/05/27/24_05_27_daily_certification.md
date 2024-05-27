# 24_05_27_daily_certification

# Side Project

### Side Project ERD CLOUD

**팀 페이지**

[ERDCloud](https://www.erdcloud.com/team/KnojjqmjLmZMWR2gL)

**ERD**

[Side Project](https://www.erdcloud.com/d/oXWj5rnsrJpDmDrWF)

### ERD CLOUD 사용법

[☁️ ERD CLOUD - ERD 다이어그램을 온라인에서 그려보자](https://inpa.tistory.com/entry/ERD-CLOUD-☁️-ERD-다이어그램을-온라인에서-그려보자)

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 선분 위의 점**

[https://www.codetree.ai/missions/8/problems/point-on-the-line-segment/description](https://www.codetree.ai/missions/8/problems/point-on-the-line-segment/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/point-on-the-line-segment/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens : StringTokenizer
    
    private var n = 0   //  점 개수
    private var m = 0   //  선분 개수

    private lateinit var pos : IntArray //  좌표 배열

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        pos = IntArray(n + 2)

        tokens = StringTokenizer(br.readLine())
        for(i in 1 .. n)
            pos[i] = tokens.nextToken().toInt()
        pos[n + 1] = Int.MAX_VALUE

        pos.sort()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val left = tokens.nextToken().toInt()
            val right = tokens.nextToken().toInt()

            val lower = lowerPos(left) + 1
            val upper = upperPos(right) - 1

            if(lower > upper)
                sb.append("0\n")
            else
                sb.append("${upper - lower + 1}\n")
        }

        print(sb)
    }

    //  target보다 작은 좌표 중 최대인 좌표 찾기
    private fun lowerPos(target : Int) : Int {
        var start = 0
        var end = n + 1
        var ret = 0

        while(start <= end) {
            val mid = (start + end) / 2

            if(pos[mid] < target) { //  target보다 mid의 좌표가 작을 경우
                ret = mid           //  일단 mid 저장
                start = mid + 1     //  더 오른쪽에서 찾아보기
            }
            else
                end = mid - 1   //  더 왼쪽에서 찾아보기
        }

        return ret
    }

    //  target보다 큰 좌표 중 최소인 좌표 찾기
    private fun upperPos(target : Int) : Int {
        var start = 0
        var end = n + 1
        var ret = n + 1

        while(start <= end) {
            val mid = (start + end) / 2

            if(pos[mid] > target) { //  target보다 mid의 좌표가 클 경우
                ret = mid           //  일단 mid 저장
                end = mid - 1     //  더 왼쪽에서 찾아보기
            }
            else
                start = mid + 1   //  더 오른쪽에서 찾아보기
        }

        return ret
    }
}

fun main() {
    Main().solve()
}
```

**CO{)E TREE 최단 거리 11**

[https://www.codetree.ai/missions/8/problems/shortest-distance-11/description](https://www.codetree.ai/missions/8/problems/shortest-distance-11/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/shortest-distance-11/description)

**코드** 

```kotlin
import java.util.*
import java.io.*

class Main {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens : StringTokenizer

    private var n = 0
    private var m = 0

    private lateinit var graph : Array<ArrayList<Node>>
    private lateinit var dist : LongArray

    private var a = 0
    private var b = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        graph = Array(n + 1) { ArrayList<Node>() }
        dist = LongArray(n + 1) { Long.MAX_VALUE }

        repeat(m) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toLong()

            graph[v1].add(Node(v2, cost))
            graph[v2].add(Node(v1, cost))
        }

        tokens = StringTokenizer(br.readLine())
        a = tokens.nextToken().toInt()
        b = tokens.nextToken().toInt()

        dijkstra()  //  b부터 각 지점까지 최단거리 구하기

        for(i in 1 .. n)
            graph[i].sortBy { it.v }

        var curV = a
        var curDist = 0L //  curV까지 누적 거리
        
        sb.append("${dist[a]}\n")

        while(curV != b) {
            sb.append("$curV ")

            for(next in graph[curV]) {
                val nextV = next.v  //  curV와 연결된 정점

                if(curDist + next.c + dist[nextV] == dist[a]) {   //  curV와 nextV 사이의 간선 길이 + nextV와 b 사이의 거리 = a와 b 사이의 거리이면
                    curV = nextV    //  다음 정점은 nextV
                    curDist += next.c
                    break
                }
            }
        }

        sb.append(curV)

        println(sb)
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>()

        dist[b] = 0
        pq.offer(Node(b, dist[b]))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val cv = cur.v
            val cc = cur.c

            if(dist[cv] < cc)
                continue

            for(next in graph[cv]) {
                val nv = next.v
                val nc = cur.c + next.c

                if(nc < dist[nv]) { //  cv를 거쳐서 nv까지 가는게 더 빠를 경우
                    dist[nv] = nc
                    pq.offer(Node(nv, dist[nv]))
                }
            }
        }
    }

    private class Node(val v : Int, val c : Long) : Comparable<Node> {
        override fun compareTo(other : Node) : Int {
            return this.c.compareTo(other.c)
        }
    }
}

fun main() {
    Main().solve()
}
```