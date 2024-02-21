# 24_02_22_daily_certification

```
[koreii] #53 데일리인증
20240222
알고리즘
- dijkstra 문제 풀이, 디버깅
- setter 대신 생성자로 초기화하기
```

# Problem Solving (Algorithm & SQL)

**BOJ 10473 인간 대포**

[10473번: 인간 대포](https://www.acmicpc.net/problem/10473)

그래프를 구축하기 위해 다음 세 가지 경우를 생각해야 한다.

1. **시작점 vs 끝점**
2. **시작점, 끝점 vs 대포**
3. **대포 vs 대포**

**1. 시작점 vs 끝점**

1의 경우 시작점과 끝점 모두 대포가 아니므로 서로 직선으로 오고 가기 위해선 걸어가야 한다. 따라서 두 점 사이의 최소 시간은 (두 점 사이 거리 / 걷는 속도)이다.

**2. 시작점, 끝점 vs 대포**

2의 경우 시작점, 끝점에서 대포로 가기 위해선 걸어가야 한다. 반대로 대포에서 시작점 또는 끝점으로 가려면 다음과 같은 경우가 있다.

**2-1. 대포로 2초 동안 50m를 날아가고 남은 거리를 걸어가기**

**2-2. 그냥 걸어가기**

2-1과 2-2의 시간을 비교해서 더 적은 시간이 두 점 사이의 최소 시간이다.

만약 두 점 사이의 거리가 50m 미만일 경우 (50 - 두 점 사이의 거리)는 대포에서 내린 후 되돌아서 걸어야 하는 거리이다. 어쨋든 걸어야 하는 거리의 절댓값은 두 점 사이의 거리가 50m 이상일 때의  (두 점 사이의 거리 - 50)이므로 절댓값을 이용해 시간을 계산하면 된다.

**3. 대포 vs 대포**

3의 경우 두 대포 사이를 걸어서 이동할 수도, 대포로 50m를 2초 동안 이동 후 남은 거리를 걸어서 이동할 수도 있다. 2-1, 2-2처럼 계산한 후 최소 시간을 구하면 된다.

임의의 두 점 사이의 최소 시간을 모두 구한 후 완전 그래프를 구축한 뒤에 Dijkstra를 이용하면 된다. 대포 개수 n이 최대 100이므로 점 개수는 최대 102개이다. 아마 Floyd-Warshall로도 가능할 것 같다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.*

const val WALK   = 5    //  걸어가는 속도

class BOJ_10473 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var sy = 0.0
    private var sx = 0.0
    private var ey = 0.0
    private var ex = 0.0

    private lateinit var posArr : Array<Pos>
    private lateinit var graph : Array<ArrayList<Node>>
    private lateinit var minCost : DoubleArray  //  minCost[i] : 시작 정점에서 i 정점까지의 거리

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        sy = tokens.nextToken().toDouble()
        sx = tokens.nextToken().toDouble()

        tokens = StringTokenizer(br.readLine())
        ey = tokens.nextToken().toDouble()
        ex = tokens.nextToken().toDouble()

        n = br.readLine().toInt()

        graph = Array(n + 3) { ArrayList() }

        minCost = DoubleArray(n + 3) { Double.MAX_VALUE }

        posArr = Array(n + 3) { Pos(0.0, 0.0) }
        posArr[1] = Pos(sy, sx)
        posArr[n + 2] = Pos(ey, ex)

        for(i in 2 .. n + 1) {
            tokens = StringTokenizer(br.readLine())
            posArr[i] = Pos(tokens.nextToken().toDouble(), tokens.nextToken().toDouble())
        }

        //  graph 구축
        //  시작점과 끝점은 대포가 아니므로 걸어갈 수 밖에 없음
        val distSE = dist(posArr[1], posArr[n + 2])
        graph[1].add(Node(n + 2, distSE / WALK))
        graph[n + 2].add(Node(1, distSE / WALK))

        for(i in 2 .. n + 1) {
            //  시작점과 각 대포까지 거리
            val distStart = dist(posArr[1], posArr[i])
            //  끝점과 각 대포 사이 거리
            val distEnd = dist(posArr[i], posArr[n + 2])

            //  시작점, 끝점에서 대포로 가기 위해선 걸어가야 함
            graph[1].add(Node(i, distStart / WALK))
            graph[n + 2].add(Node(i, distEnd / WALK))

            //  대포에서 시작점 또는 끝점으로 가기 위해선
            //  1. 대포로 2초 동안 날아가기 + 남는 거리는 걷기
            //  2. 그냥 걷기
            //  이 중 더 작은 시간이 둘 사이의 최소 시간
            val distStartRes = abs((distStart - 50) / WALK)
            val distEndRes = abs((distEnd - 50) / WALK)          //  50m는 대포로 2초 동안 날아가고 남은 거리 걷는 시간, 만약 dist - 50이 음수이면 대포로 날아간 다음에 돌아 걸어가야함

            graph[i].add(Node(1, (distStartRes + 2).coerceAtMost(distStart / WALK)))
            graph[i].add(Node(n + 2, (distEndRes + 2).coerceAtMost(distEnd / WALK)))
        }

        //  각 대포 사이의 최소 이동 시간
        for(i in 2 .. n) {
            for(j in i + 1 .. n + 1) {
                val distCannon = dist(posArr[i], posArr[j]) //  두 대포 i, j 사이 거리

                //  대포 사이 이동하기 위해선
                //  1. 대포로 2초 동안 날아가기 + 남은 거리는 걷기
                //  2. 그냥 걷기
                val distCannonRes = abs((distCannon - 50) / WALK)   //  50m는 대포로 날아가고 남은 거리 걷는 시간
                val minCost = (2 + distCannonRes).coerceAtMost(distCannon / WALK)

                graph[i].add(Node(j, minCost))
                graph[j].add(Node(i, minCost))
            }
        }

        //  dijkstra algorithm
        dijkstra()

        println(minCost[n + 2])
    }

    private fun dijkstra() {
        val pq = PriorityQueue<Node>() { it1, it2 -> it1.cost.compareTo(it2.cost) }
        minCost[1] = 0.0

        pq.offer(Node(1, minCost[1]))

        while(pq.isNotEmpty()) {
            val cur = pq.poll()
            val curV = cur.v        //  최단 시간이 알려진 정점
            val curCost = cur.cost  //  시작 정점으로부터 curV까지의 최소 시간

            if(minCost[curV] < curCost)
                continue

            for(next in graph[curV]) {
                val nextV = next.v  //  curV와 인접한 정점
                val nextCost = curCost + next.cost  //  시작 정점으로부터 curV까지 최소 시간 + curV에서 nextV까지 가는 최소 시간

                if(minCost[nextV] > nextCost) { //  기존에 알려진 nextV까지의 최소 시간보다 curV를 거쳐서 가는게 더 빠를 경우
                    minCost[nextV] = nextCost   //  nextV까지의 최소 시간 갱신
                    pq.offer(Node(nextV, minCost[nextV]))
                }
            }
        }
    }

    private class Pos(var y : Double, var x : Double)

    private fun dist(p1 : Pos, p2 : Pos) : Double {
        return sqrt((p1.y - p2.y) * (p1.y - p2.y) + (p1.x - p2.x) * (p1.x - p2.x))
    }

    private class Node(val v : Int, var cost : Double)
}

fun main() {
    BOJ_10473().solve()
}
```

Dijkstra 알고리즘 자체는 어렵지 않았지만, 그래프 구축이 꽤 구현할 양이 많았고, 그 과정에서 실수도 있었다.

구체적으로 posArr[n + 2]의 y, x 값을 입력받을 때 실수가 있었다.

```kotlin
posArr[n + 2].y = ey
posArr[n + 2].x = ex
```

이렇게 y, x 값을 설정해야 했는데

```kotlin
posArr[n + 2].y = ey
posArr[n + 2].y = ex
```

이렇게 y, x 값을 설정해서 x 값이 0이 되어 정답을 오랫동안 도출하지 못했고, 일일이 println으로 출력해보면서 겨우 오류를 발견할 수 있었다.

```kotlin
posArr[1] = Pos(sy, sx)
posArr[n + 2] = Pos(ey, ex)
```

위의 정답 코드처럼 생성자 방식을 이용하면 setter 방식으로 값을 설정하는 것보다 오류의 가능성을 줄일 수 있을 것 같다. 알고리즘 풀이 뿐만 아니라 Spring 개발에서도 @Builder를 사용하여 보다 안전한 개발을 했던 경험이 있는데 이와 비슷한 경우인 것 같다.