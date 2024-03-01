# 24_03_01_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 9241 바이러스 복제**

[9241번: 바이러스 복제](https://www.acmicpc.net/problem/9241)

**두 문자열을 앞에서부터 비교해서 공통된 부분의 길이 s, 뒤에서부터 비교해서서 공통된 부분의 길이 e를 구한다.**

**예제 1**

s = 1, e = 2

![ex1.jpeg](24_03_01_daily_certification%20cee2990ac4694ac281e87cc9b46b86b8/ex1.jpeg)

A에서 공통된 부분을 제외한 부분(AA)을 제거하고 B에서 공통된 부분을 제외한 부분(GCG)를 삽입하면 된다. 따라서 정답은 3이다.

**예제 2**

![ex2_1.jpeg](24_03_01_daily_certification%20cee2990ac4694ac281e87cc9b46b86b8/ex2_1.jpeg)

s = 7, e = 1

![ex2_2.jpeg](24_03_01_daily_certification%20cee2990ac4694ac281e87cc9b46b86b8/ex2_2.jpeg)

A에서 공통된 부분을 제외한 부분(ACACAT)을 제거하고 B에서 공통된 부분을 제외한 부분(CACA)를 삽입하면 된다. 제거하는 개수는 포함되지 않는다. 따라서 정답은 4다.

**게시판 예제**

주의 사항은 앞에서부터 공통된 부분과 뒤에서부터 공통된 부분이 겹치지 않게 해야 한다.

![ex3_1.jpeg](24_03_01_daily_certification%20cee2990ac4694ac281e87cc9b46b86b8/ex3_1.jpeg)

s = 3, e = 3이 아니라,

![ex3_2.jpeg](24_03_01_daily_certification%20cee2990ac4694ac281e87cc9b46b86b8/ex3_2.jpeg)

s = 3, e = 0 이 맞다.

![ex4.jpeg](24_03_01_daily_certification%20cee2990ac4694ac281e87cc9b46b86b8/ex4.jpeg)

s = 2, e = 2

문자열 B에서 앞에서부터 공통된 부분, 뒤에서부터 공통된 부분을 제외한 부분의 길이가 삽입할 부분 길이이다.

### 코드

```kotlin
import java.util.*
import java.io.*

class BOJ_9241 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var s = 0
    private var e = 0

    fun solve() {
        val dnaA = br.readLine()
        val dnaB = br.readLine()

        var aIdx = 0
        var bIdx = 0

        //  dnaA, dnaB 앞에서부터 같은 부분 개수 찾기
        while(aIdx < dnaA.length && bIdx < dnaB.length && dnaA[aIdx] == dnaB[bIdx]) {
            s++
            aIdx++
            bIdx++
        }

        aIdx = dnaA.length - 1
        bIdx = dnaB.length - 1

        //  dnaA, dnaB 뒤에서부터 같은 부분 개수 찾기
        while(aIdx >= s && bIdx >= s && dnaA[aIdx] == dnaB[bIdx]) {
            e++
            aIdx--
            bIdx--
        }

        println(dnaB.length - s - e)
    }
}

fun main() {
    BOJ_9241().solve()
}
```