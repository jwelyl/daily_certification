# 24_03_03_daily_certification

```
[koreii] #63 데일리인증
20240303
알고리즘
- DFS | BFS 문제 풀이
```

# Problem Solving (Algorithm & SQL)

**BOJ 1375 나이**

[1375번: 나이](https://www.acmicpc.net/problem/1375)

정점의 개수가 적다면 Floyd-Warshall 알고리즘으로 O(N^3)의 시간 복잡도의 전처리 후 모든 임의의 두 쌍에 대해 O(1)의 시간 복잡도로 나이 비교가 가능하겠지만, 안타깝게도 N이 최대 1,000,000이라 Floyd-Warshal 알고리즘은 사용할 수 없다. 결국 인접 리스트로 그래프를 만들고 탐색을 통해 구해야 한다.

쿼리 개수 Q가 크다면 한 번의 탐색 후 모든 쿼리를 처리할 수 있도록 해야겠지만, Q가 최대 20이므로 매번 임의의 두 정점 사이를 탐색하도록 하면 된다. 

**실패한 코드 (NumberFormatException)**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<Long, Int>()  //  이름 : Long, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private var find = false                                  //  dfs 완료 시 find = true
    private val depth = IntArray(MAX) { -1 }                  // -1이면 루트, 그 이후부터 0, 1, 2, ...
    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken().toLong()
            val name2 = tokens.nextToken().toLong()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            depth[v2] = if(depth[v2] < depth[v1] + 1) depth[v1] + 1 else depth[v2]
            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken().toLong()
            val name2 = tokens.nextToken().toLong()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            if(depth[v1] == depth[v2])  //  둘이 루트로부터 상대적인 깊이가 같으면 비교할 수 없음
                sb.append("gg ")
            else {  //  상대적인 깊이가 다를 경우, 깊이가 더 깊은 쪽에서 거슬러 올라가다 깊이가 더 얕은 쪽을 만나면 깊이가 얕은 쪽의 나이가 더 많음
                val pred = if(depth[v1] < depth[v2]) v1 else v2 //  더 나이 많을 수 있는 쪽
                val succ = if(depth[v1] < depth[v2]) v2 else v1 //  더 나이 어릴 수 있는 쪽

                find = false
                Arrays.fill(visited, false)

                sb.append("${if(dfs(succ, pred)) pred else "gg"} ")
            }
        }

        println(sb)
    }

    private fun dfs(cur : Int, target : Int) : Boolean {
        if(find)    //  이미 target에 도달했을 경우, 더 이상 탐색 안해도 되므로 종료
            return true

        if(cur == target) {   //  target에 도달했을 경우
            find = true
            return true
        }

        visited[cur] = true

        for(next in graph[cur]) {
            if(!visited[next] && dfs(next, target)) //  next부터 탐색했을 때 target에 도달했으면
                return true //  true 반환
        }

        return false    //  결국 찾지 못하면 false 반환
    }
}

fun main() {
    BOJ_1375().solve()
}
```

예제에서 이름이 수로 되어 있고 길이가 6byte라 해서 크기가 최대 6byte인 정수를 이름으로 받는 줄 알았다. 알고 보니 예제의 이름이 수로 된 문자열인 것이고 결국 String으로 받아야 한다. 또한 정점 번호는 Map을 통해 풀이 중 임의로 정한 것이므로 결국엔 원래 주어진 이름으로 출력해야 한다.

**틀린 코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<String, Int>()  //  이름 : String, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private var find = false                                  //  dfs 완료 시 find = true
    private val depth = IntArray(MAX) { -1 }                  // -1이면 루트, 그 이후부터 0, 1, 2, ...
    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            depth[v2] = if(depth[v2] < depth[v1] + 1) depth[v1] + 1 else depth[v2]
            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if(nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if(nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            if(depth[v1] == depth[v2])  //  둘이 루트로부터 상대적인 깊이가 같으면 비교할 수 없음
                sb.append("gg ")
            else {  //  상대적인 깊이가 다를 경우, 깊이가 더 깊은 쪽에서 거슬러 올라가다 깊이가 더 얕은 쪽을 만나면 깊이가 얕은 쪽의 나이가 더 많음
                val pred = if(depth[v1] < depth[v2]) v1 else v2 //  더 나이 많을 수 있는 쪽
                val succ = if(depth[v1] < depth[v2]) v2 else v1 //  더 나이 어릴 수 있는 쪽
                val predName = if(depth[v1] < depth[v2]) name1 else name2

                find = false
                Arrays.fill(visited, false)

                sb.append("${if(dfs(succ, pred)) predName else "gg"} ")
            }
        }

        println(sb)
    }

    private fun dfs(cur : Int, target : Int) : Boolean {
        if(find)    //  이미 target에 도달했을 경우, 더 이상 탐색 안해도 되므로 종료
            return true

        if(cur == target) {   //  target에 도달했을 경우
            find = true
            return true
        }

        visited[cur] = true

        for(next in graph[cur]) {
            if(!visited[next] && dfs(next, target)) //  next부터 탐색했을 때 target에 도달했으면
                return true //  true 반환
        }

        return false    //  결국 찾지 못하면 false 반환
    }
}

fun main() {
    BOJ_1375().solve()
}
```

처음 그래프를 만드는 과정에서 그래프의 각 정점의 상대적인 깊이를 정하고 매 쿼리에서 깊이가 깊은 쪽에서 깊이가 얕은 쪽으로 탐색을 했었다. 정확한 이유는 모르겠지만, 논리적인 비약이 존재하는 것 같다.

결국에는 a b에서 a에서 b로 도달 가능한지, b에서 a로 도달 가능한지 둘 다 확인해봐야 한다.

**또 틀린 코드(DFS)**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<String, Int>()  //  이름 : String, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private var find = false                                  //  dfs 완료 시 find = true
    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if (nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if (nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1 = nameMap.getOrDefault(name1, -1)
            val v2 = nameMap.getOrDefault(name2, -1)    //  비교 관계에서 나타난 적 없는 이름이면 -1

            if(v1 == -1 || v2 == -1)    //  둘 중 하나라도 알려진 게 없으면 비교 불가
                sb.append("gg ")
            else {  //  상대적인 깊이가 다를 경우, 깊이가 더 깊은 쪽에서 거슬러 올라가다 깊이가 더 얕은 쪽을 만나면 깊이가 얕은 쪽의 나이가 더 많음
                Arrays.fill(visited, false)
                find = false

                if (dfs(v1, v2)) {  //  v1부터 거슬러 올라가 v2에 도달할 수 있을 경우
//                    println("if")
                    sb.append("$name2 ") //  name2가 더 나이 많음
                }
                else {
                    Arrays.fill(visited, false)
                    find = false

//                    println("elif")
                    if (dfs(v2, v1))  //  v2부터 거슬러 올라가 v1에 도달할 수 있을 경우
                        sb.append("$name1 ") //  name1이 더 나이 많음
                    else
                        sb.append("gg ")
                }
            }
        }

        println(sb)
    }

    private fun dfs(cur: Int, target: Int): Boolean {
//        println("dfs($cur, $target)")

        if (find)    //  이미 target에 도달했을 경우, 더 이상 탐색 안해도 되므로 종료
            return true

        if (cur == target) {   //  target에 도달했을 경우
            find = true
            return true
        }

        visited[cur] = true

        for (next in graph[cur]) {
            if (!visited[next] && dfs(next, target)) //  next부터 탐색했을 때 target에 도달했으면
                return true //  true 반환
        }

        return false    //  결국 찾지 못하면 false 반환
    }
}

fun main() {
    BOJ_1375().solve()
}
```

DFS로 a에서 b로 도달 가능한지 확인하는 코드를 작성했는데 결국 또 틀렸다. 몇 번을 고쳐봐도 59%의 벽을 넘지 못하고 있는데 무슨 일일까.

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_1375 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var vertex = 1
    private val nameMap = HashMap<String, Int>()  //  이름 : String, 정점 : Int
    private var n = 0   //  정점 개수
    private var m = 0   //  나이 비교 횟수
    private var q = 0   //  질의 개수

    private val visited = BooleanArray(MAX)                   //  방문 배열
    private var graph = Array(MAX) { ArrayList<Int>() }       //  그래프

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()

        repeat(m) {
            tokens = StringTokenizer(br.readLine())

            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1: Int
            val v2: Int

            if (nameMap.contains(name1))
                v1 = nameMap[name1]!!
            else {
                v1 = vertex++
                nameMap[name1] = v1
            }

            if (nameMap.contains(name2))
                v2 = nameMap[name2]!!
            else {
                v2 = vertex++
                nameMap[name2] = v2
            }

            graph[v2].add(v1)
        }

        q = br.readLine().toInt()

        repeat(q) {
            tokens = StringTokenizer(br.readLine())
            val name1 = tokens.nextToken()
            val name2 = tokens.nextToken()

            val v1 = nameMap.getOrDefault(name1, -1)
            val v2 = nameMap.getOrDefault(name2, -1)    //  비교 관계에서 나타난 적 없는 이름이면 -1

            if(v1 == -1 || v2 == -1)    //  둘 중 하나라도 알려진 게 없으면 비교 불가
                sb.append("gg ")
            else {  //  한 쪽에서부터 시작해서 다른 쪽에 도달 가능하면, 다른 쪽이 더 나이가 많은 것임
                if(bfs(v1, v2))     //  v1에서부터 시작해서 v2에 도달 가능하면
                    sb.append("$name2 ")    //  name2가 name1보다 나이가 많음
                else if(bfs(v2, v1))    //  v2에서부터 시작해서 v1에 도달 가능하면
                    sb.append("$name1 ")    //  name1이 name2보다 나이가 많음
                else
                    sb.append("gg ")
            }
        }

        println(sb)
    }

    private fun bfs(from : Int, to : Int) : Boolean {
        val q = LinkedList<Int>()
        Arrays.fill(visited, false)

        visited[from] = true
        q.offer(from)

        while(q.isNotEmpty()) {
            val cur = q.poll()  //  현재 정점

            for(next in graph[cur]) {   //  주변 다음 정점 탐색
                if(visited[next])   //  이미 방문한 적 있으면 skip
                    continue

                if(next == to)  //  다음 정점이 목적 정점일 경우
                    return true //  찾음

                visited[next] = true    //  다음 정점부터 탐색
                q.offer(next)
            }
        }   //  while-end

        return false    //  찾지 못함
    }   //  bfs-end
}

fun main() {
    BOJ_1375().solve()
}
```

결국 BFS로 풀어서 맞았다.

**생각해볼 문제**

1. DFS로 했을 때 왜 틀리는 건지, DFS 코드 어떤 게 잘못됐는지
2. 상대적으로 깊이를 파악해서 비교하는게 어떤 점에서 틀렸는지