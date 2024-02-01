# 24_02_01_daily_certification

# Computer Network

## Network Layer

### DHCP

![1.jpeg](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/1.jpeg)

![2.jpg](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/2.jpg)

![3.jpg](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/3.jpg)

# Problem Solving (Algorithm & SQL)

**BOJ 11509 풍선 맞추기**

[11509번: 풍선 맞추기](https://www.acmicpc.net/problem/11509)

높이 별로 맞출 수 있는 화살 개수를 저장하는 배열 arrCnt를 선언한다. 풍선 높이 h를 입력받을 때마다 해당 높이에 화살이 1개 이상 있을 경우, 즉 arrCnt[h] > 0일 경우, arrCnt[h]를 1 감소시키고, arrCnt[h - 1]을 1 증가시킨다. 만약 arrCnt[h]가 0일 경우, 해당 높이를 맞출 수 있는 화살이 없으므로 정답을 1 증가시키고 arrCnt[h - 1]을 1 증가시킨다. 

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_11509 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    
    private var n = 0    //    풍선 개수
    private val arrowCnt = IntArray(MAX)    //    arrowCnt[i] : 높이 i에 있는 화살 개수
    private var ans = 0
    
    fun solve() {
        n = br.readLine().toInt()
        
        tokens = StringTokenizer(br.readLine())
        repeat(n) {
            val ballon = tokens.nextToken().toInt()    //    풍선 높이
            
            if(arrowCnt[ballon] > 0)    //    해당 높이를 맞출 수 있는 화살이 있을 경우
                arrowCnt[ballon]--            //    해당 높이 풍선을 터뜨림
            else    //    해당 높이를 맞출 수 있는 화살이 없을 경우
                ans++    //    화살 하나를 해당 높이로 새로 쏨
            arrowCnt[ballon - 1]++        //    높이 1 감소
        }
        
        println(ans)
    }    
}

fun main() {
    BOJ_11509().solve()
}
```