# 24_01_11_daily_certification

# Computer Network

## TCP Flow Control

![IMG_B404371FE253-1.jpeg](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/IMG_B404371FE253-1.jpeg)

## TCP Connection Management

[](https://chat.openai.com/share/463f5e2e-e879-47a9-babe-a9009aff1650)

### TCP 3-Way Handshake

Client와 Server의 TCP 연결을 맺기 위한 과정

![IMG_D4030722AD17-1.jpeg](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/IMG_D4030722AD17-1.jpeg)

![IMG_DB2619D36845-1.jpeg](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/IMG_DB2619D36845-1.jpeg)

### TCP 4-Way Handshake

Client와 Server의 TCP 연결을 종료하기 위한 과정

![4wayhandshake.png](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/4wayhandshake.png)

[4-Way Handshake](https://hojunking.tistory.com/107)

# Problem Solving (Algorithm & SQL)

**BOJ 19542 전단지 돌리기**

[19542번: 전단지 돌리기](https://www.acmicpc.net/problem/19542)

각 노드에서 리프 노드 사이의 거리의 최댓값을 구하고 그 값이 D 이상인 노드 개수를 구하면 된다. 이 때 루트 노드는 제외한다. DFS로 구할 수 있다.

루트 노드에서 해당 노드들을 모두 탐색하고 돌아오기 위해서 루트 노드와 이어진 간선을 두번씩 탐색해야 한다. 

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_19542 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var s = 0
    private var d = 0

    private lateinit var tree : Array<MutableList<Int>>
    private lateinit var visited : BooleanArray
    private lateinit var maxDist : IntArray         //  maxDist[i] : i 노드로부터 리프노드까지 거리 중 최댓값

    private var cnt = 0     //  어떤 리프 노드로부터 d 이상 떨어진 노드 개수 (루트 노드 제외)

    fun solve() {
        tokens = StringTokenizer(br.readLine())

        n = tokens.nextToken().toInt()
        s = tokens.nextToken().toInt()
        d = tokens.nextToken().toInt()

        tree = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)
        maxDist = IntArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            tree[v1].add(v2)
            tree[v2].add(v1)
        }

        dfs(s)

        for(i in 1 .. n) {
            if(maxDist[i] >= d && i != s)
                cnt++
        }

        print(2 * cnt)
    }

    private fun dfs(v : Int) {
        var max = 0 //  자식 노드들의 리프노드까지의 거리 중 최댓값
        var cnt = 0 //  자식 노드 개수

        visited[v] = true

        for(nv in tree[v]) {
            if(!visited[nv]) {
                cnt++
                dfs(nv)
                max = max.coerceAtLeast(maxDist[nv])
            }
        }

        if(cnt != 0)    //  리프노드가 아닐 경우
            maxDist[v] = max + 1
    }
}

fun main() {
    BOJ_19542().solve()
}
```