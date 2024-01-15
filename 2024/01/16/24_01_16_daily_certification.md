# 24_01_16_daily_certification

# Computer Network

## Application Layer

### Overview

![IMG_103F3E8C6336-1.jpeg](24_01_16_daily_certification%208588f1f5dd47458c85bbe1e87b252b2e/IMG_103F3E8C6336-1.jpeg)

### Transport Layer가 Application Layer에 제공하는 서비스

- TCP
    - Reliable Transport
    - Flow Control
    - Congestion Control
    - Connection-oriented
- UDP
    - Unreliable data transfer

### HTTP (HyperText Transfer Protocol)

- **Definition**
    - **HyperText : Link가 있는 text (다른 Object를 reference할 수 있음)**
    - HyperText를 전송하는 프로토콜이 HTTP
- **구성 요소**
    
    ![IMG_7DDF76F14663-1.jpeg](24_01_16_daily_certification%208588f1f5dd47458c85bbe1e87b252b2e/IMG_7DDF76F14663-1.jpeg)
    
    - **Request** : Client인 Web Browser가 Web Server로 전송
    - **Response** : Web Server가 Browser의 Request를 받아서 응답으로 보냄
- TCP
    - HTTP도 Application Layer의 Protocol이므로 Transport Layer의 서비스를 받음
    - HTTP는 TCP Service를 기반으로 동작함.
    - TCP Connection이 생성되어야 함
- **Stateless**
    - Client로부터 Request를 받고 Response를 보내준 다음에 Client의 Request에 대한 어떤 정보도 기억하지 않음.
    - 빠르고 단순함
- HTTP Connections
    - Non-persistent HTTP
        
        매번 Request, Response마다 TCP Connection을 새로 맺어줌
        
    - Persistent HTTP
        
        한 번 맺은 TCP Connection을 재활용함
        
- HTTP Request, Response Message
    - HTTP Request Message
    - HTTP Response Message, Status Code

### Cookie

HTTP의 Stateless한 성질을 보완하기 위한 Trick (Stateful한 특성이 필요할 경우)

![IMG_4020064E55FD-1.jpeg](24_01_16_daily_certification%208588f1f5dd47458c85bbe1e87b252b2e/IMG_4020064E55FD-1.jpeg)

### Web Caches (Proxy Server)

![IMG_9CAEBA12172C-1.jpeg](24_01_16_daily_certification%208588f1f5dd47458c85bbe1e87b252b2e/IMG_9CAEBA12172C-1.jpeg)

Client의 요청을 Origin Server에 바로 보내지 않고 Proxy Server(Web Caches)에 보내어 캐시되었을 경우 빠르게 요청에 대한 응답을 가져옴 (응답 시간 감소)

캐시되어있지 않을 경우 Proxy Server에서 Origin Server로 요청을 보내어 응답을 가져온 후 Client에 보내줌

Web Proxy의 단점? 이슈? → 보안? 물론 있음

Cache의 단점? Origin Server의 원본이 변경되었을 때 일관성을 어떻게 유지할 것인가?

# Problem Solving (Algorithm & SQL)

**BOJ 3973 Time To Live**

[3973번: Time To Live](https://www.acmicpc.net/problem/3973)

아래 문제와 유사한 트리 지름 문제이다. 1월 7일에 해결했다.

[12896번: 스크루지 민호](https://www.acmicpc.net/problem/12896)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_3973 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var c = 0   //  테스트 케이스 개수

    private var n = 0   //  트리 정점 개수
    private lateinit var tree : Array<MutableList<Int>> //  트리
    private lateinit var visited : BooleanArray

    private var root = 0
    private var diameter = 0
    private var endVertex = 0

    fun solve() {
        c = br.readLine().toInt()

        repeat(c) {
            n = br.readLine().toInt()

            tree = Array(n) { mutableListOf() }
            visited = BooleanArray(n)

            repeat(n - 1) {
                tokens = StringTokenizer(br.readLine())
                val v1 = tokens.nextToken().toInt()
                val v2 = tokens.nextToken().toInt()

                tree[v1].add(v2)
                tree[v2].add(v1)
            }

            dfs(root, 0)
            Arrays.fill(visited, false)
            root = endVertex
            dfs(root, 0)

            sb.append("${(diameter + 1) / 2}\n")

            root = 0
            diameter = 0
            endVertex = 0
        }

        print(sb)
    }

    private fun dfs(v : Int, dist : Int) {
        visited[v] = true

        if(dist > diameter) {
            diameter = dist
            endVertex = v
        }

        for(nv in tree[v]) {
            if(!visited[nv])
                dfs(nv, dist + 1)
        }
    }
}

fun main() {
    BOJ_3973().solve()
}
```