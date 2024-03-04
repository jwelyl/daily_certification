# 24_03_05_daily_certification

# 주요 대기업 지원 준비

[주요 기업 서류 지원 준비](https://www.notion.so/ca46129d4559498794f2f81c628d75a5?pvs=21)

# Problem Solving (Algorithm & SQL)

## 코딩 테스트 대비 프로그래머스 문제

[프로그래머스 복습할 문제](https://www.notion.so/1163cecfe9474113bd7ced09c4af4d19?pvs=21)

**BOJ 19641 중첩 집합 모델**

[19641번: 중첩 집합 모델](https://www.acmicpc.net/problem/19641)

![ex.jpeg](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/ex.jpeg)

방문 순서 order를 최초 1로 정해놓고, 루트 노드부터 DFS로 탐색하면 된다. DFS로 노드 v를 탐색할 때는 v를 방문처리한 다음에 v의 left 값을 order로 놓고 order를 1 증가시킨다. v의 인접 노드 중 아직 탐색하지 않은 노드들이 자식 노드들이 되므로 자식 노드들을 DFS로 재귀적으로 탐색한다. 모든 자식 노드들을 탐색한 후에 v의 right 값을 order로 놓고 order를 1 증가시키면 된다.

![ex1.jpeg](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/ex1.jpeg)

루트가 정해지면 root로부터 DFS를 하면 된다. 인접 리스트로 트리를 구현했을텐데(정점 개수가 최대 10^5개이므로 그래야만 한다.) 각 정점 별 인접 리스트를 오름차순으로 정렬하여 자식 정점 중 번호가 작은 정점부터 탐색하도록 한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_19641 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens : StringTokenizer
    
    private var n = 0        //    노드 개수
    private var root = 0     //    루트 노드
    
    private lateinit var left : IntArray     //    left[i] : i번째 노드의 left 값
    private lateinit var right : IntArray    //    right[i] : i번째 노드의 right 값
    
    private lateinit var tree : Array<ArrayList<Int>>    //    중첩 집합 모델 트리
    private lateinit var visited : BooleanArray
    
    private var order = 1    //    방문 순서
    
    fun solve() {
        n = br.readLine().toInt()
        
        left = IntArray(n + 1)
        right = IntArray(n + 1)
        tree = Array(n + 1) { ArrayList() }
        visited = BooleanArray(n + 1)
        
        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            
            val node = tokens.nextToken().toInt()    //    간선 정보 받을 노드
            
            while(tokens.hasMoreTokens()) {
                val adjNode = tokens.nextToken().toInt()    //    해당 노드와 연결된 노드
                
                if(adjNode == -1)
                    break
                
                tree[node].add(adjNode)
                tree[adjNode].add(node)    //    양방향
            }
            
            tree[node].sort()    //    노드의 인접 노드들을 오름차순으로 정렬
        }
        
        root = br.readLine().toInt()
        dfs(root)    //    루트부터 탐색
        
        repeat(n) {
            sb.append("${it + 1} ${left[it + 1]} ${right[it + 1]}\n")
        }
        
        print(sb)
    }
    
    private fun dfs(v : Int) {
        visited[v] = true
        left[v] = order++    //    방문한 정점 v의 left에 현재 순서 기록
    
        for(nv in tree[v]) {    //    v의 인접 정점 nv
            if(!visited[nv])
                dfs(nv)    //    아직 방문하지 않은 정점 nv를
        }
        
        right[v] = order++    //    인접 정점 모두 방문한 다음 순서를 v의 right에 기록
    }   // dfs-end
}

fun main() {
    BOJ_19641().solve()
}
```