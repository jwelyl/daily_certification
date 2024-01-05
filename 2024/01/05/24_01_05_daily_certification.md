# 24_01_05_daily_certification

```
[koreii] #5 데일리인증
20240105
Compuer Network
- TCP/UDP Demultiplexing 보충
- UDP Header 필드 학습
- RDT Protocol 학습
알고리즘
- 그래프 이론 (단순 경로, 사이클, 트리) & DFS로 사이클에 속한 정점 찾기 문제 해결 + 난이도 기여
```

# Computer Network

## Multiplexing vs Demultiplexing (보충)

![IMG_A1BEF1DAA789-1.jpeg](24_01_05_daily_certification%20e96e233e549b49cea76bd8a6521cc788/IMG_A1BEF1DAA789-1.jpeg)

- UDP Demux (Connectionless)
    - segment의 dst IP, dst Port가 같으면 같은 Socket으로 들어옴
- TCP Demux (Connection-Oriented)
    - segment의 src IP, src Port, dst IP, dst Port가 모두 같아야 같은 Socket으로 들어옴
    - Sender와 Receiver의 연결 지향
    
    cf) src, dst IP 정보는 Packet의 header에 존재
    

## TCP/UDP Header

### UDP Header

4개 Field (단순함)

- src port : 16bits (port num : 0 ~ 65535)
- dst port : 16bits (port num : 0 ~ 65535)
- length
- checksum : 전송 도중 에러 발생 판단 필드 (에러 발생 시 drop)

UDP도 최소한 mux/demux와 Error Checking은 함. 메시지를 받았다면 에러가 없음은 보장 가능

![IMG_0080.jpg](24_01_05_daily_certification%20e96e233e549b49cea76bd8a6521cc788/IMG_0080.jpg)

### Reliable Data Transfer (RDT) Protocol

# Problem Solving (Algorithm & SQL)

**BOJ 20530 양분**

[20530번: 양분](https://www.acmicpc.net/problem/20530)

**그래프 이론(사이클, 트리, 단순 경로) + 깊이 우선 탐색**

N개의 정점을 가진 트리에 간선이 하나 추가되면 반드시 사이클이 1개 발생한다는 것을 알아야 했다.

단순 사이클 유무 판별은 기본적인 DFS로 쉽게 판별 가능하지만 **사이클에 포함된 정점을 찾는 것**은 좀 더 많은 응용이 필요하다.

사이클을 찾은 후에 사이클에 포함된 정점들을 대표로 해당 정점에 연결된, 사이클에 포함되지 않는 정점들과 그룹화한다. DFS를 이용하면 쉽게 그룹화 가능하다.

**서로 같은 그룹에 속한 두 정점 사이에는 단순 경로가 1개, 서로 다른 그룹에 속한 두 정점 사이에는 단순 경로가 2개 있다.**

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_20530 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var q = 0
    private lateinit var graph: Array<MutableList<Int>>

    private lateinit var visited: BooleanArray
    private val isCycle = HashSet<Int>()
    private lateinit var finished: BooleanArray
    private lateinit var parents: IntArray
    private lateinit var groups: IntArray
    private var group = 1

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        q = tokens.nextToken().toInt()

        graph = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)
        finished = BooleanArray(n + 1)
        parents = IntArray(n + 1)
        groups = IntArray(n + 1)

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            graph[v1].add(v2)
            graph[v2].add(v1)
        }

        //  사이클에 속한 정점들 찾기
        findCycle(1, 0)

        for (v in isCycle) { //  사이클에 속한 정점들을 대표로 해당 정점과 연결되며 사이클에 포함되지 않는 정점들을 그룹으로 묶음
            findGroup(v)
            group++
        }

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            sb.append("${if (groups[v1] == groups[v2]) 1 else 2}\n")
        }

        print(sb)
    }

    private fun findGroup(cv: Int) {
        groups[cv] = group

        for (nv in graph[cv]) {
            if (!isCycle.contains(nv) && groups[nv] == 0)
                findGroup(nv)
        }
    }

    private fun findCycle(cv: Int, parent: Int) {
        visited[cv] = true

        for (nv in graph[cv]) {
            if (!visited[nv]) {
                parents[nv] = cv
                findCycle(nv, cv)
            } else if (!finished[nv] && nv != parent)
                checkCycle(cv, nv)
        }

        finished[cv] = true
    }

    private fun checkCycle(cv: Int, root: Int) {
        isCycle.add(cv)
        if (cv == root)
            return

        checkCycle(parents[cv], root)
    }
}

fun main() {
    BOJ_20530().solve()
}
```