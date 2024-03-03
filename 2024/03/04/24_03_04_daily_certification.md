# 24_03_04_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 2220 힙 정렬**

[2220번: 힙 정렬](https://www.acmicpc.net/problem/2220)

말로 설명하기 정말 어렵다. 그래도 그리디하게 생각해보면 힙에서 poll할 때마다 swap 횟수가 최대가 되려면 최솟값 1이 가장 마지막 index에 존재해야 한다는 것을 알 수 있다. 그래야 최댓값을 poll한 이후 최솟값 1이 루트에 위치하게 되어 제 위치를 찾는 과정에서 swap이 최대한 많이 발생한다.

또한 poll 후 1이 가장 마지막 index에 위치해야 다음 poll에서도 swap이 최대한 많이 발생한다.

N = 6일 때 swap이 최대인 최대 힙을 생성하는 과정은 다음과 같다. 핵심은 i는 1부터 n-1까지 순서대로 index i에 i+1을 배치하고 루트로 올리면서 그 경로상에 존재하는 더 작은 값들과 위치를 바꾸는 것이다. 

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%201.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%202.png)

완성된 최대 힙에서 6을 poll을 해보자. 1이 제 자리를 찾아 움직이는 과정이 6이 루트를 향해 움직이는 경로와 정 반대인 것을 알 수 있다.

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%203.png)

즉 삭제 시 1이 가장 마지막 index로 위치 조정되는 과정의 역과정으로 힙을 만들면 된다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2220 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()

    private var n = 0
    private lateinit var maxHeap : IntArray

    fun solve() {
        n = br.readLine().toInt()

        maxHeap = IntArray(n + 1)

        for(i in 1 ..<n) { //  i + 1 값 위치 정하기
            var p = i

            while(p > 1) {
                maxHeap[p] = maxHeap[p / 2] //  i + 1을 index = 1로 올리면서 이전에 위치했던 값들이랑 자리 바꾸기
                p /= 2
            }

            maxHeap[1] = i + 1  //  최대 힙이므로 index = 1이 현재까지 값 중 최댓값이어야 함
        }

        maxHeap[n] = 1  //  swap이 많으려면 최솟값 1이 삭제 시 index = 1로 와야 swap이 최대가 된다.

        for(i in 1 .. n)
            sb.append("${maxHeap[i]} ")

        print(sb)
    }
}

fun main() {
    BOJ_2220().solve()
}
```