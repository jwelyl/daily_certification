# 24_07_18_daily_certification

# Operating System

# Problem Solving (Algorithm & SQL)

### BOJ 2539 모자이크

[](https://www.acmicpc.net/problem/2539)

```kotlin
import java.util.*
import java.io.*

class BOJ_2539 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var h = 0
    private var w = 0

    private var m = 0    //    사용할 수 있는 색종이 개수
    private var n = 0    //    덮어야 하는 점 개수

    private var start = 0
    private var end = 0

    private lateinit var points : IntArray    //    x 좌표만 저장하면 됨

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        h = tokens.nextToken().toInt()
        w = tokens.nextToken().toInt()

        end = maxOf(h, w)    //    한 장이면 모든 점 덮을 수 있음

        m = br.readLine().toInt()
        n = br.readLine().toInt()

        points = IntArray(n)

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            val y = tokens.nextToken().toInt()
            val x = tokens.nextToken().toInt()

            start = maxOf(start, y)    //    도화지 밑면에 색종이 맞춰서 붙이므로 세로로 가장 멀리 떨어진 점만큼의 길이는 확보해야 함

            points[it] = x
        }

        println(parametricSearch())
    }

    private fun parametricSearch() : Int {
        var ret = end    //    가장 작은 색종이 크기

        points.sort()    //    x 좌표 크기 순 정렬

        while(start <= end) {
            val mid = (start + end) / 2    //    한 변 길이가 mid인 색종이

            val cnt = count(mid)    //    한 변 길이가 mid일 때 덮을 수 있는 점의 개수

            if(cnt < n)    //    n개 점을 한 변 길이가 mid인 n개의 색종이로 다 덮을 수 없을 경우
                start = mid + 1    //    색종이 한 변 길이 키워야 함
            else {    //    다 덮을 수 있을 경우
                ret = mid        //    일단 mid를 정답으로 저장
                end = mid - 1    //    크기 더 줄여보기
            }
        }

        return ret
    }

    //    색종이 한 변 길이가 size일 때 m개의 색종이로 덮을 수 있는 점의 개수
    private fun count(size : Int) : Int {
        var ret = 0    //    덮을 수 있는 점의 수
        var cnt = 1    //    사용한 종이의 수
        var startIdx = 0

        while(cnt <= m) {
            val minX = points[startIdx]
            val maxX = minX + size - 1

            for(idx in startIdx ..< n) {
                if(points[idx] in minX .. maxX)    //    idx번째 점이 cnt번째 색종이로 덮을 수 있는 점이면
                    ret++
                else {    //    idx번째 점이 cnt번째 점으로 덮을 수 없는 점이면
                    cnt++    //    다음 종이로 덮어야 함
                    startIdx = idx    //    다음 종이로 idx번째 점부터 덮기 시작함
                    break
                }
            }

            if(ret == n)    //    m개 종이 다 쓰지 않아도 덮을 수 있는 경우
                break
        }

        return ret
    }
}

fun main() {
    BOJ_2539().solve()
}
```