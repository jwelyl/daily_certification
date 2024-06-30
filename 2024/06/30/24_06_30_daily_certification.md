# 24_06_30_daily_certification

# Network

# Network Layer(L3)

# Subnet Mask, CIDR

## Subnet Mask

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled.png)

## Classless Inter-Domain Routing (CIDR)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%201.png)

# Broadcast IP Address

**32bit IPv4 Address 중 마지막 8bit가 모두 1일 경우, 즉 xxx.xxx.xxx.255일 경우, Broadcast IP이다.**

48bit MAC Address 모든 bit가 1일 경우, 즉 FF-FF-FF-FF-FF-FF일 경우, Broadcast MAC Address이다.

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%202.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%203.png)

# Loopback  IP Address

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%204.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%205.png)

# Internet, Router

## Internet

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%206.png)

## TTL, Fragmentation

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%207.png)

![fragmentation.png](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/fragmentation.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%208.png)

# DHCP

## Internet Configuration

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%209.png)

## Dynamic Host Configuration Protocol (DHCP)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%2010.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%2011.png)

# Address Resolution Protocol (ARP)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%2012.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%2013.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%2014.png)

# Ping, RTT

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%2015.png)

![Untitled](Computer%20Science%200cbe1a4d2b0342d4a6f134ab5951701e/Network%20ebfd8b33cb7644568bfa788183d4bd66/Network%20Layer(L3)%20653ec6e197c24a2ea94dbe55b6ba109e/Untitled%2016.png)

# Problem Solving (Algorithm & SQL)

### BOJ 4343 **Arctic Network**

[](https://www.acmicpc.net/problem/4343)

```kotlin
import java.util.*
import java.io.*
import kotlin.math.pow
import kotlin.math.sqrt

class BOJ_4343 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var tc = 0
    private var s = 0
    private var p = 0

    private lateinit var posArr : Array<IntArray>
    private lateinit var parents : IntArray
    private val edgeList = ArrayList<Edge>()

    fun solve() {
        tc = br.readLine().toInt()

        repeat(tc) {
            tokens = StringTokenizer(br.readLine())
            s = tokens.nextToken().toInt()
            p = tokens.nextToken().toInt()

            edgeList.clear()

            posArr = Array(p + 1) { IntArray(2) }
            parents = IntArray(p + 1) { it }

            for(i in 1 .. p) {
                tokens = StringTokenizer(br.readLine())
                posArr[i][0] = tokens.nextToken().toInt()
                posArr[i][1] = tokens.nextToken().toInt()
            }

            for(v1 in 1 ..< p) {
                for(v2 in v1 + 1 .. p)
                    edgeList.add(Edge(v1, v2, sqrt((posArr[v2][0] - posArr[v1][0]).toDouble().pow(2) + (posArr[v2][1] - posArr[v1][1]).toDouble().pow(2))))
            }

            sb.append(String.format("%.2f\n", kruskal()))
        }

        print(sb)
    }

    private fun findDS(v : Int) : Int {
        if(v == parents[v])
            return v

        parents[v] = findDS(parents[v])
        return parents[v]
    }

    private fun union(v1 : Int, v2 : Int) {
        val v1DS = findDS(v1)
        val v2DS = findDS(v2)

        if(v1DS != v2DS)
            parents[v2DS] = v1DS
    }

    //  s개는 이미 위성과 연결되어 있음
    //  이미 위성과 연결된 s개를 하나의 정점으로 생각하면, p -s + 1개를 연결하면 됨
    //  p - s개의 간선을 가지는 최소 비용 신장 트리 생성하기
    private fun kruskal() : Double {
        var ret = 0.0
        var cnt = 0

        edgeList.sort()

        for(edge in edgeList) {
            val v1 = edge.v1
            val v2 = edge.v2
            val cost = edge.cost

            if(findDS(v1) != findDS(v2)) {
                union(v1, v2)
                cnt++

                if(cnt == p - s) {
                    ret = cost
                    break
                }
            }
        }

        return ret
    }

    private class Edge(val v1 : Int, val v2 : Int, val cost : Double) : Comparable<Edge> {
        override fun compareTo(other: Edge): Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    BOJ_4343().solve()
}
```