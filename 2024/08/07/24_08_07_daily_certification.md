# 24_08_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14601 **샤워실 바닥 깔기 (Large)**

[](https://www.acmicpc.net/problem/14601)

```kotlin
import java.util.*
import java.io.*

const val EMPTY = 0
const val HOLE = -1

class BOJ_14601 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var k = 0
    private var holeY = 0
    private var holeX = 0

    private lateinit var board : Array<IntArray>
    private var num = 1

    fun solve() {
        k = br.readLine().toInt()

        board = Array(1 shl k) { IntArray(1 shl k)}
        tokens = StringTokenizer(br.readLine())
        holeX = tokens.nextToken().toInt() - 1
        holeY = tokens.nextToken().toInt() - 1

        board[holeY][holeX] = HOLE

        recursion(0, 0, (1 shl k) - 1, (1 shl k) - 1, 1 shl k, holeY, holeX)

        for(r in (1 shl k) - 1 downTo 0) {
            for(c in 0 ..< (1 shl k))
                sb.append(board[r][c]).append(" ")
            sb.append("\n")
        }
        print(sb)
    }

    private fun recursion(sy : Int, sx : Int, ey : Int, ex : Int, size : Int, holeY : Int, holeX : Int) {
        if(size == 2) { //  2 * 2 정사각형 채우기
            for(r in sy .. ey) {
                for(c in sx .. ex) {
                    if(board[r][c] == EMPTY)
                        board[r][c] = num
                }
            }

            num++
            return
        }

        val my = (sy + ey) / 2
        val mx = (sx + ex) / 2

        if(holeY in (sy .. my)) {
            //  구멍이 1 사분면에 존재할 경우
            if(holeX in (sx .. mx)) {
                //  중앙의 2, 3, 4 사분면을 미리 채우기
                board[my][mx + 1] = num
                board[my + 1][mx] = num
                board[my + 1][mx + 1] = num++

                recursion(sy, sx, my, mx, size / 2, holeY, holeX)
                recursion(sy, mx + 1, my, ex, size / 2, my, mx + 1)
                recursion(my + 1, sx, ey, mx, size / 2, my + 1, mx)
                recursion(my + 1, mx + 1, ey, ex, size / 2, my + 1, mx + 1)
            }
            //  구멍이 2사분면에 존재할 경우
            else {
                //  중앙의 1, 3, 4 사분면을 미리 채우기
                board[my][mx] = num
                board[my + 1][mx] = num
                board[my + 1][mx + 1] = num++

                recursion(sy, sx, my, mx, size / 2, my, mx)
                recursion(sy, mx + 1, my, ex, size / 2, holeY, holeX)
                recursion(my + 1, sx, ey, mx, size / 2, my + 1, mx)
                recursion(my + 1, mx + 1, ey, ex, size / 2, my + 1, mx + 1)
            }
        }
        else {
            //  구멍이 3사분면에 존재할 경우
            if(holeX in (sx ..< sx + size / 2)) {
                //  중앙의 1, 2, 4 사분면을 미리 채우기
                board[my][mx] = num
                board[my][mx + 1] = num
                board[my + 1][mx + 1] = num++

                recursion(sy, sx, my, mx, size / 2, my, mx + 1)
                recursion(sy, mx + 1, my, ex, size / 2, my, mx + 1)
                recursion(my + 1, sx, ey, mx, size / 2, holeY, holeX)
                recursion(my + 1, mx + 1, ey, ex, size / 2, my + 1, mx + 1)
            }
            //  구멍이 4사분면에 존재할 경우
            else {
                //  중앙의 1, 2, 3 사분면을 미리 채우기
                board[my][mx] = num
                board[my][mx + 1] = num
                board[my + 1][mx] = num++

                recursion(sy, sx, my, mx, size / 2, my, mx + 1)
                recursion(sy, mx + 1, my, ex, size / 2, my, mx + 1)
                recursion(my + 1, sx, ey, mx, size / 2, my + 1, mx)
                recursion(my + 1, mx + 1, ey, ex, size / 2, holeY, holeX)
            }
        }
    }
}

fun main() {
    BOJ_14601().solve()
}
```