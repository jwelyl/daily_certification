# 24_08_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1662 압축

[](https://www.acmicpc.net/problem/1662)

```kotlin
import java.io.*

class BOJ_1662 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    
    fun solve() {
        val input = br.readLine()

        println(recursion(input))
    }

    private fun recursion(input : String) : Int {
        if(input == "")
            return 0

        var len = 0

        var idx = 0

        while(idx < input.length) {
            val ch = input[idx]

            if(ch in '0' .. '9') {  //  문자가 숫자일 경우
                if(idx + 1 < input.length && input[idx + 1] == '(') {   //  압축된 문자열의 계수를 찾았을 경우
                    val startIdx = idx + 1  //  시작 괄호 위치
                    var endIdx = idx + 2    //  끝 괄호 위치

                    var startP  = 1         //  시작 괄호 개수
                    var endP = 0            //  끝 괄호 개수

                    while(startP != endP) {
                        if(input[endIdx] == '(')
                            startP++
                        else if(input[endIdx] == ')')
                            endP++
                        endIdx++
                    }

                    val q = if(endIdx - startIdx == 1) "" else input.substring(startIdx + 1, endIdx - 1)

                    len += (ch.code - '0'.code) * recursion(q)
                    idx = endIdx
                }
                else {  //  계수가 아닌 단순 숫자일 경우
                    len++
                    idx++
                }
            }
        }

        return len
    }
}

fun main() {
    BOJ_1662().solve()
}
```