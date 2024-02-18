# 24_02_19_daily_certification

```
[koreii] #50 데일리인증
20240219
알고리즘
- Longest Common Subsequence 길이 구하기 + 실제 LCS 찾아서 출력하기
- Longest Common Subsequence vs Longest Common Substring 구분
```

# Problem Solving (Algorithm & SQL)

**BOJ 17218 비밀번호 만들기**

[17218번: 비밀번호 만들기](https://www.acmicpc.net/problem/17218)

LCS를 실제로 출력하는 문제이다. 

문제에서 요구하는 LCS가 Longest Common Subsequence인지, Longest Common Substring인지 잘 확인하고 문제를 풀어야 한다.

**Longest Commnon Subsequence**

가장 긴 공통 부분 수열을 구하는 것이다. 부분 수열인만큼 꼭 연속해서 고를 필요가 없다. 이번 문제에서 요구하는 것이 이 LCS이다.

이 문제의 예시처럼

AUTABBEHNSA와 BCUAMEFKAJNA의 Longest Common Subsequence는

A**U**T**A**BB**E**H**N**S**A** 와 BC**UA**M**E**FKAJ**NA** 이므로 **UAENA**이다. 연속해서 선택하지 않았음을 알 수 있다.

[9251번: LCS](https://www.acmicpc.net/problem/9251)

**Longest Commnon Substring**

가장 긴 공통 부분 문자열을 구하는 것이다. 부분 문자열인만큼 반드시  연속해서 골라야 한다. 위의 예시에서 길이 2 이상의 공통 부분 문자열은 없다.

[5582번: 공통 부분 문자열](https://www.acmicpc.net/problem/5582)

가장 긴 공통 부분 수열을 구하는 것으로 돌아와서 길이를 구하는 것은 9251번처럼 O(len1 * len2)의 시간 복잡도로 한 번에  쉽게 구할 수 있다. 하지만 실제 LCS를 출력하기 위해서는 길이만 저장할 뿐만 아니라 추가적으로 O(len1 * len2)의 공간복잡도를 사용해서 현재 (i, j)까지 최장 공통 부분 수열 이전에 어디서 LCS가 생성되는지를 저장하고 LCS가 완성된 다음에 역추적 과정을 거쳐야 한다.

![32123231.jpeg](24_02_19_daily_certification%2018fd3a4ed0174877ba013d65915e1fa8/32123231.jpeg)

**코드**

```kotlin
import java.util.*
import java.io.*

const val UL = 1
const val L  = 2
const val U  = 3

class BOJ_17218 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private lateinit var str1 : CharArray
    private lateinit var str2 : CharArray

    private lateinit var dp : Array<IntArray>
    private lateinit var dir : Array<IntArray>  //  dir[i][j] : (i, j)의 이전 칸이 어디였는지 저장

    private val dy = intArrayOf(0, -1, 0, -1)
    private val dx = intArrayOf(0, -1, -1, 0)

    private val stack = Stack<Char>()

    fun solve() {
        str1 = br.readLine().toCharArray()
        str2 = br.readLine().toCharArray()

        dp = Array(str1.size + 1) { IntArray(str2.size + 1) }
        dir = Array(str1.size + 1) { IntArray(str2.size + 1) }

        for(i in 1 .. str1.size) {
            for(j in 1 .. str2.size) {
                if(str1[i - 1] == str2[j - 1]) {    //  str1[i-1]과 str2[j-1]이 같을 경우
                    dp[i][j] = dp[i - 1][j - 1] + 1 //  str1[i-2]까지와 str2[j-2]까지의 최장 공통 길이 + 1이 str[i-1]까지와 str2[j-1]까지의 최장 공통 길이임
                    dir[i][j] = UL  //  str1[i-1]와 str2[i-1]은 str1[i-2]와 str2[i-2]의 LCS에서 파생됨
                }
                else {
                    if(dp[i][j - 1] >= dp[i - 1][j]) {  //  str1[i-1]까지와 str2[j-2]까지의 LCS가 가장 길 경우
                        dp[i][j] = dp[i][j - 1]         //  str1[i-1]까지와 str2[j-2]까지의 최장 공통 길이 + 1이 str1[i-1]까지와 str2[j-1]까지의 최장 공통 길이
                        dir[i][j] = L   //  str1[i-1]와 str2[i-1]은 str1[i-1]와 str2[i-2]의 LCS에서 파생됨
                    }
                    else {                             //  str1[i-2]까지와 str2[j-1]까지의 LCS가 가장 길 경우
                        dp[i][j] = dp[i - 1][j]        //  str1[i-2]까지와 str2[j-1]까지의 최장 공통 길이 + 1이 str1[i-1]까지와 str2[j-1]까지의 최장 공통 길이
                        dir[i][j] = U   //  str1[i-1]와 str2[i-1]은 str1[i-2]와 str2[i-1]의 LCS에서 파생됨
                    }
                }
            }
        }

        //  가장 끝에서부터 역추적
        var cy = str1.size
        var cx = str2.size

        //  cy = 0이거나, cx = 0이면 dir[cy][cx] = 0임
        while(dir[cy][cx] != 0) {   //  끝에 도달하기 전까지
            val dir = dir[cy][cx]

            val ny = cy + dy[dir]
            val nx = cx + dx[dir]

            if(dir == UL)   //  LCS 길이가 1 증가할 때마다
                stack.add(str1[cy - 1]) //  추가된 공통 부분 문자열의 문자 스택에 추가

            cy = ny
            cx = nx
        }

        while(stack.isNotEmpty())   //  스택 내용 출력
            sb.append(stack.pop())

        print(sb)
    }
}

fun main() {
    BOJ_17218().solve()
}
```