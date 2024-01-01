# 24.01.01.mon 데일리 인증

# Problem Solving (Algorithm & SQL)

**BOJ 1025 제곱수 찾기**

[1025번: 제곱수 찾기](https://www.acmicpc.net/problem/1025)

**브루트포스 알고리즘**

첫 번째로 고를 숫자의 위치 (y, x)를 정하고, y축 공차 dy, x축 공차 dx를 각각 정하여 가능한 다음 숫자를 골라서 수를 만든다.

고르는 숫자가 추가될 때마다 제곱수인지 체크하고 이전 최대 완전제곱수와 비교해야 한다. 끝까지 고른다고 최대 완전 제곱수가 되는 것이 아님에 주의

dy, dx 모두 0일 경우 처음 고른 숫자 하나만 존재한다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.sqrt

class BOJ_1025 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var m = 0

    private lateinit var nums : Array<CharArray>
    private var maxSn = -1

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        nums = Array(n) { CharArray(m) }
        for(i in 0 ..< n)
            nums[i] = br.readLine().toCharArray()

        for(y in 0 ..< n) {
            for(x in 0 ..< m) {
                //  초항 : nums[y][x]
                for(dy in -n + 1 ..< n) {   //  y축 공차
                    for(dx in -m + 1 ..< m) {   //  x축 공차
                        if(dy == 0 && dx == 0) {  //  y축 공차, x축 공차 모두 0일 경우, 한 자릿수임
                            if(isSquareNum(nums[y][x].code - '0'.code))
                                maxSn = maxSn.coerceAtLeast(nums[y][x].code - '0'.code)

                            continue
                        }

                        val num = StringBuilder()
                        for(k in 0 .. Int.MAX_VALUE) {
                            val ny = y + dy * k
                            val nx = x + dx * k

                            if(isIn(ny, nx)) {
                                num.append(nums[ny][nx])

                                if(isSquareNum(num.toString().toInt()))
                                    maxSn = maxSn.coerceAtLeast(num.toString().toInt())
                            }
                            else break
                        }
                    }
                }
            }
        }

        print(maxSn)
    }

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< n && x in 0 ..< m
    }

    private fun isSquareNum(num : Int) : Boolean {
        for(root in 0 .. sqrt(num.toDouble()).toInt()) {
            if(root * root == num)
                return true
        }

        return false
    }
}

fun main() {
    BOJ_1025().solve()
}
```
