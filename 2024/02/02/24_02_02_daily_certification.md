# 24_02_02_daily_certification

# Computer Network

## Network Layer

### Routing vs Forwarding

![Untitled](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/Untitled.png)

### **Forwarding**

**라우터의 가장 중요한 기능**

- 라우터가 패킷 받았을 때, 패킷 안에 있는 헤더의 dst IP address를 보고, dst IP address와 Forwarding Table의 entry와 매칭시켜서  가장 적합한 IP address로 보내는 것 **(Longest Prefix Matching)**
- Forwarding 자체는 단순히 Forwarding Table Look up임

### **Routing**

**Forwarding Table을 만드는 것 (Routing Algorithm을 통해)**

각 라우터에서 다른 라우터로 가장 최적의 경로를 찾아서 Forwarding Table에 기록함

Router를 Node로, Link를 Edge로 보면 Network는 Graph이고, Routing Algorithm은 Shortest Path Algorithm임

### Routing Algorithm

1. Link State Algorithm (전체 Network Graph가 알려져 있는 경우, Global)
2. Distance Vector Algorithm (이웃 라우터와 정보를 주고받으며 최단 경로를 구하는 경우, Decentralized)

### Link State Algorithm

**Dijkstra Algorithm**

![IMG_0127.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0127.jpg)

![IMG_0128.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0128.jpg)

![IMG_0129.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0129.jpg)

![IMG_0130.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0130.jpg)

![IMG_0131.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0131.jpg)

![IMG_0132.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0132.jpg)

![IMG_0133.jpg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/IMG_0133.jpg)

Link State Algorithm을 위해서는 모든 Router에 Network 상황을 Broadcast해야 한다.

**그 범위는 특정 Network로 한다.** (ex) 서강대학교 네트워크), 전체 Internet이 아님

**각 네트워크 주체별로(ex) HYU, SK) 각자의 Routing Algorithm을 사용함**

![1.jpeg](24_02_02_daily_certification%20e3eb2c8d1d3d4372965145c48a7f5c1f/1.jpeg)

네트워크와 네트워크 사이(ex) Sogang과 SKKU 사이)에도 Router가 존재하는데 네트워크와 네트워크 사이의 Routing Algorithm은? 즉 Network 사이의 Routing Algorithm은?

### Distance Vector Algorithm

**Bellman-Ford Algorithm**

# Problem Solving (Algorithm & SQL)

**BOJ 4792 레드 블루 스패닝 트리**

[4792번: 레드 블루 스패닝 트리](https://www.acmicpc.net/problem/4792)

빨간 간선을 우선적으로 사용하여 스패닝 트리를 만들고 빨간 간선만으로 부족할 때 파란 간선까지 사용해 스패닝 트리를 만든다. 이 때 사용한 파란 간선 개수를 cnt1이라 하자. 만약 cnt1 > k이면, 파란 간선이 정확히 k개인 스패닝 트리는 존재할 수 없다.

반대로 파란 간선을 우선적으로 사용하여 스패닝 트리를 만들고 파란 간선만으로 부족할 때 빨간 간선까지 사용하여 스패닝 트리를 만든다. 이 때 사용한 파란 간선 개수를 cnt2라고 하자. 만약 cnt2 < k이면, 파란 간선이 정확히 k개인 스패닝 트리는 존재할 수 없다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.collections.ArrayList

class BOJ_4792 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  정점 개수
    private var m = 0   //  간선 개수
    private var k = 0   //  요구 파란 간선 개수

    private lateinit var ds : IntArray

    private val blueEdges = ArrayList<Edge>()
    private val redEdges = ArrayList<Edge>()

    fun solve() {
        while(true) {
            tokens = StringTokenizer(br.readLine())
            n = tokens.nextToken().toInt()
            m = tokens.nextToken().toInt()
            k = tokens.nextToken().toInt()

            if(n == 0 && m == 0 && k == 0) {
                print(sb)
                break
            }

            blueEdges.clear()
            redEdges.clear()
            ds = IntArray(n + 1) { it }

            repeat(m) {
                tokens = StringTokenizer(br.readLine())
                val color = tokens.nextToken()[0]
                val v1 = tokens.nextToken().toInt()
                val v2 = tokens.nextToken().toInt()

                if(color == 'B')
                    blueEdges.add(Edge(v1, v2))
                else
                    redEdges.add(Edge(v1, v2))
            }

            //  1. 빨강 간선 먼저 사용해서 MST 구축
            var mstEdgeCnt = 0
            var blueEdgeCnt = 0

            for(redEdge in redEdges) {
                val v1 = redEdge.v1
                val v2 = redEdge.v2

                val v1DS = findDS(v1)
                val v2DS = findDS(v2)

                if(v1DS != v2DS) {
                    unionDS(v1DS, v2DS)
                    mstEdgeCnt++
                }

                if(mstEdgeCnt == n - 1)
                    break
            }

            if(mstEdgeCnt < n - 1) {
                for(blueEdge in blueEdges) {
                    val v1 = blueEdge.v1
                    val v2 = blueEdge.v2

                    val v1DS = findDS(v1)
                    val v2DS = findDS(v2)

                    if(v1DS != v2DS) {
                        unionDS(v1DS, v2DS)
                        mstEdgeCnt++
                        blueEdgeCnt++
                    }

                    if(mstEdgeCnt == n - 1)
                        break
                }
            }

            if(blueEdgeCnt > k) {   //  빨간 간선 먼저 사용했는데도 파란 간선이 k개가 넘을 경우
                sb.append("0\n")
                continue
            }
            //  2. 파란 간선 먼저 사용해서 MST 구축
            ds = IntArray(n + 1) { it }

            mstEdgeCnt = 0
            blueEdgeCnt = 0

            for(blueEdge in blueEdges) {
                val v1 = blueEdge.v1
                val v2 = blueEdge.v2

                val v1DS = findDS(v1)
                val v2DS = findDS(v2)

                if(v1DS != v2DS) {
                    unionDS(v1DS, v2DS)
                    mstEdgeCnt++
                    blueEdgeCnt++
                }

                if(mstEdgeCnt == n - 1)
                    break
            }

            if(blueEdgeCnt < k) {   //  파란 간선을 먼저 사용했음에도 파란 간선이 k개가 되지 않을때
                sb.append("0\n")
                continue
            }

            sb.append("1\n")
        }
    }

    private fun findDS(v : Int) : Int {
        if(v == ds[v]) return v

        ds[v] = findDS(ds[v])
        return ds[v]
    }

    private fun unionDS(v1 : Int, v2 : Int) {
        ds[v2] = v1
    }

    private class Edge(val v1 : Int, val v2 : Int)
}

fun main() {
    BOJ_4792().solve()
}
```