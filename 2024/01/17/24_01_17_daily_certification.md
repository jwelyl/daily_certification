# 24_01_17_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 2642 전개도**

[2642번: 전개도](https://www.acmicpc.net/problem/2642)

전개도의 한 면에서 마주보는 면이 존재하려면 처음 인접한 면이 있는 방향으로 2번 이동했을 때 면이 존재해야 한다. 예를 들어 1번 면의 오른쪽에 다른 면이 있다면 → 방향으로 두 번 이동해서 도달할 수 있는 면이 있으면 마주보는 면이 존재한다. 이러한 면이 각 면마다 단 1개만, 모든 면에 대해 존재해야 전개도가 완성된다.

전개도를 만드는 방법을 알았으면 DFS로 잘 구현하면 된다. 방법만 알면 쉽게 풀리는 문제는 아니고 DFS 구현도 보기보다 까다로운 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2645 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val map = Array(6) { IntArray(6) }
    private val visited = Array(6) { BooleanArray(6) }
    private val posArr = Array<Pos>(7) { Pos(0, 0) }    //  각 숫자 면의 위치
    private val opposite = IntArray(7)  //  opposite[i] : i가 적인 면의 반대편 면의 숫자

    fun solve() {
        for(i in 0 ..< 6) {
            tokens = StringTokenizer(br.readLine())
            for(j in 0 ..< 6) {
                val num = tokens.nextToken().toInt()
                map[i][j] = num

                if(num in 1 .. 6)
                    posArr[num] = Pos(i, j)
            }
        }

        for(num in 1 .. 6) {
            if (!solve(num)) {
                println(0)
                return
            }

        }
        
        println(opposite[1])
    }

    private fun solve(num : Int) : Boolean {
        var dirCnt = 0  //  시작 방향으로 2번 갈 수 있는 방향 수, 1이어야 함

        val posY = posArr[num].y
        val posX = posArr[num].x

        for(initDir in 0 ..< 4) {
            for (i in 0..< 6)
                Arrays.fill(visited[i], false)
            
            if(dfs(num, posY, posX, initDir, 0))    //  num에서 initDir 방향으로 2번 가서 다른 면을 만날 수 있을 경우
                dirCnt++
        }

        return dirCnt == 1
    }

    //  num이 적힌 위치 (posY, posX)에서 initDir 방향으로 2칸 가서 도달할 수 있는 칸이 있으면 true
    private fun dfs(num : Int, posY : Int, posX : Int, initDir : Int, initDirCnt : Int) : Boolean {
        visited[posY][posX] = true

        if(initDirCnt == 2) {   //  2번만에 왔을 경우
            opposite[num] = map[posY][posX]
            return true
        }

        if(initDirCnt == 0) {
            val nPosY = posY + dy[initDir]
            val nPosX = posX + dx[initDir]

            if(!isIn(nPosY, nPosX) || map[nPosY][nPosX] == 0 || visited[nPosY][nPosX])
                return false

            return dfs(num, nPosY, nPosX, initDir, 1)
        }

        for(d in 0 ..< 4) {
            val nPosY = posY + dy[d]
            val nPosX = posX + dx[d]

            if(!isIn(nPosY, nPosX) || map[nPosY][nPosX] == 0 || visited[nPosY][nPosX])
                continue

            val add = if(initDir == d) 1 else 0
            val result = dfs(num, nPosY, nPosX, initDir, initDirCnt + add)

            if(result) 
                return true
        }

        return false
    }

    //  우 : 0, 하 : 1, 좌 : 2, 상 : 3
    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< 6 && x in 0 ..< 6
    }

    private class Pos(val y : Int, val x : Int)
}

fun main() {
    BOJ_2645().solve()
}
```