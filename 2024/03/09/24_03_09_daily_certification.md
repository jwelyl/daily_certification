# 24_03_09_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 31464 초콜릿 괴도 코코 (Sweet)**

[31464번: 초콜릿 괴도 코코 (Sweet)](https://www.acmicpc.net/problem/31464)

이미 초콜릿들이 하나의 연결요소로 연결되어 있고 사이클도 존재하는 상태가 주어진다. 이 중 어떤 하나의 초콜릿을 제거했을때 나머지 초콜릿들은 여전히 연결요소로 연결되어 있지만 사이클은 존재하지 않게 되는 초콜릿을 찾아야 한다.

**예제 1**

![ex1.jpeg](24_03_09_daily_certification%20cc97ee70c44a469da62bb3d9afb0c2f0/ex1.jpeg)

**예제 2**

![ex2.jpeg](24_03_09_daily_certification%20cc97ee70c44a469da62bb3d9afb0c2f0/ex2.jpeg)

초콜릿이 있는 칸을 탐색할 때 이전 초콜릿 칸도 같이 전달한다. **다음 칸을 탐색할 때, 이미 방문한 칸이면서 이전 칸이 아닐 경우 사이클이 발생**했다는 의미이다.

 

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_31464 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    
    private var n = 0
    private lateinit var map : Array<CharArray>
    private lateinit var visited : Array<BooleanArray>
    
    private var isCycle = false       //    사이클 존재 여부
    private var chocoCnt = 0    //    초콜릿 개수
    private var afterCnt = 0    //    초콜릿 하나 제거 후 연결된 초콜릿 개수
    private var ansCnt = 0      //    정답 개수
    
    fun solve() {
        n = br.readLine().toInt()
        map = Array(n) { CharArray(n) }
        visited = Array(n) { BooleanArray(n) }
        
        for(i in 0 ..< n) {
            map[i] = br.readLine().toCharArray()
            for(j in 0 ..< n) {
                if(map[i][j] == '#')
                    chocoCnt++
            }
        }
        
        for(i in 0 ..< n) {
            for(j in 0 ..< n) {
                if(map[i][j] == '#') { //    초콜릿일 경우
                    map[i][j] = '.'    //    초콜릿 제거해봄
                    
                    if(isOk()) {    //    (i, j)의 초콜릿을 제거해도 나머지 chocoCnt-1개의 초콜릿은 연결되어 있고, 사이클은 존재해서는 안됨.
                        ansCnt++
                        sb.append("${i + 1} ${j + 1}\n")
                    }
                    
                    map[i][j] = '#'    //    원상복구
                }    
            }
        }
        
        sb.insert(0, "$ansCnt\n")
        print(sb)
    }
    
    private fun isOk() : Boolean {
        isCycle = false                    //    사이클 여부 초기화
        afterCnt = 0                       //    초콜릿 하나 제거 후 연결된 초콜릿 개수 초기화
        
        for(i in 0 ..< n)
            Arrays.fill(visited[i], false)        //    방문 상태 초기화
        
        OUTER@
        for(i in 0 ..< n) {
            for(j in 0 ..< n) {
                if(map[i][j] == '#') {    //    초콜릿을 발견한 경우
                    dfs(i, j, i, j)
                    break@OUTER
                }
            }
        }
        
        //    연결된 초콜릿 개수가 원래 초콜릿 개수 - 1개이고 사이클이 없으면 true
        return afterCnt == chocoCnt - 1 && !isCycle
    }
    
    private fun dfs(y : Int, x : Int, prevY : Int, prevX : Int) {
        afterCnt++    //    연결된 초콜릿 개수 1 증가
        visited[y][x] = true    //    방문 처리
        
        for(d in 0 ..< 4) {
            val ny = y + dy[d]
            val nx = x + dx[d]    //    인접한 다음 칸
            
            if(!isIn(ny, nx) || map[ny][nx] == '.')    //    범위 밖이거나 초콜릿이 아닐 경우
                continue
            
            if(!visited[ny][nx])    //    다음 칸이 방문한 칸일 경우
                dfs(ny, nx, y, x)   //    다음 칸의 이전 칸을 현재 칸으로 하고 방문함
            else if(ny != prevY || nx != prevX)    //    다음 칸이 이미 방문한 칸인데 부모 칸이 아닐 경우, 사이클 발생함
                isCycle = true
        }
    }
    
    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)
    
    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< n && x in 0 ..< n
    }
}

fun main() {
    BOJ_31464().solve()
}
```