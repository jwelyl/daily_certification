# 24_03_13_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 9202 Boggle**

[9202번: Boggle](https://www.acmicpc.net/problem/9202)

트라이에 모든 단어를 넣고 4 x 4 보드에서 모든 격자를 출발점으로 해서 모든 가능한 단어 조합을 찾아서 트라이에 존재하는 단어면 점수를 계산한다.

![boggle.jpeg](24_03_13_daily_certification%207acdfdffd70940c48e490a239088318a/boggle.jpeg)

**실패한 코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_9202 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var w = 0   //  단어 개수
    private var trie = Trie()   //  트라이

    private var b = 0   //  보드 개수
    private val board = Array(4) { CharArray(4) }
    private val visited = Array(4) { BooleanArray(4) }

    private val findSet = HashSet<String>() //  한 Boggle에서 찾은 단어
    private var longestWord = ""    //  Boggle에서 찾은 가장 긴 단어
    private var maxScore = 0        //  Boggle에서 얻은 최대 점수

    fun solve() {
        w = br.readLine().toInt()

        repeat(w) {
            val word = br.readLine()
            trie.insert(word)   //  트라이에 단어 저장
        }

        br.readLine()

        b = br.readLine().toInt()

        repeat(b) {
            for(i in 0 ..< 4) {
                board[i] = br.readLine().toCharArray()
                Arrays.fill(visited[i], false)
            }

            if(it < b - 1)
                br.readLine()

            findSet.clear()
            longestWord = ""
            maxScore = 0

            for(i in 0 ..< 4) {
                for(j in 0 ..< 4)
                    dfs(i, j, StringBuilder().append(board[i][j]))  //  모든 경우에 대해 단어 찾기
            }

            for(word in findSet) {  //  찾은 단어
                maxScore += when(word.length) {
                    1, 2 -> 0
                    3, 4 -> 1
                    5 -> 2
                    6 -> 3
                    7 -> 5
                    8 -> 11
                    else -> 0
                }

                if(word.length > longestWord.length)
                    longestWord = word
            }

            sb.append("$maxScore $longestWord ${findSet.size}\n")
        }

        print(sb)
    }

    private fun dfs(y : Int, x : Int, word : StringBuilder) {
        val str = word.toString()

        val find = trie.find(str)
        if(find == -1)    //  부분 문자열로도 존재하지 않을 경우
            return  //  탐색 종료

        if(find == 1)   //  딱 알맞는 단어가 존재할 경우
            findSet.add(str)    //  찾은 단어 추가, 더 길게 일치하는 단어 찾기

        visited[y][x] = true    //  현재 칸 방문 처리

        for(d in 0 ..< 8) {
            val ny = y + dy[d]
            val nx = x + dx[d]  //  다음 칸

            if(isIn(ny, nx) && !visited[ny][nx]) {  //  다음 칸이 범위 내에 있고 방문하지 않았으면
                word.append(board[ny][nx])  //  다음 칸의 문자를 끝에 추가함
                dfs(ny, nx, word)   //  문자 추가한 단어 찾기
                word.deleteCharAt(word.length - 1)  //  탐색 종료 후 다음 칸 문자 제거
            }
        }

        visited[y][x] = false   //  현재 칸 방문 취소
    }

    private val dy = intArrayOf(0, 1, 1, 1, 0, -1, -1, -1)
    private val dx = intArrayOf(1, 1, 0, -1, -1, -1, 0, 1)

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< 4 && x in 0 ..< 4
    }

    private class TrieNode {
        val children = HashMap<Char, TrieNode>()
        var isLast = false
    }

    private class Trie {
        val root = TrieNode()

        /**
         * Trie에 str 추가
         */
        fun insert(str : String) {
            var cur = root  //  트라이의 현재 노드

            for(ch in str) {    //  추가하려는 문자열의 모든 문자에 대하여
                if(!cur.children.contains(ch))  //  현재 문자를 가지는 자식 노드가 없을 경우
                    cur.children[ch] = TrieNode()   //  현재 문자를 가지는 자식 노드 추가
                cur = cur.children[ch]!!    //  현재 문자를 가지는 자식 노드를 현재 노드로 함
            }

            cur.isLast = true   //  마지막 노드임
        }

        /**
         * Trie에서 str 찾기
         * 아예 존재하지 않을 경우 -1  -> ABCDE가 저장되었는데 ABD를 찾는 경우
         * 부분 문자열로 존재할 경우 0 -> ABCDE가 저장되었는데 ABC를 찾는 경우
         * 정확히 일치하게 존재할 경우 1 -> ABCDE가 저장되었는데 ABCDE를 찾는 경우
         */
        fun find(str : String) : Int {
            var cur = root  //  트라이의 현재 노드

            for(ch in str) {
                if(!cur.children.contains(ch))  //  str이 포함되지 않을 경우
                    return -1

                cur = cur.children[ch]!!    //  str의 현재 문자를 가지는 자식 노드로
            }

            return if(cur.isLast) 1 else 0  //  str 자체가 존재할 경우 1, str이 부분 문자열로만 존재할 경우 0
        }

        fun clear() {   //  Trie 전체를 지움
            clear(root) //  루트 노드 포함, 모든 자식 노드를 지움, 초기화
        }

        private fun clear(cur : TrieNode) { //  cur 노드 포함, 모든 자식 노드를 지움
            val entries = cur.children.entries

            for(entry in entries) { //  모든 자식 노드에 대해
                val child = entry.value
                clear(child)    //  자식 노드를 지움
            }

            cur.children.clear()    //  현재 노드를 지움
        }
    }
}

fun main() {
    BOJ_9202().solve()
}
```
