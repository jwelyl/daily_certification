# 24_08_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 12012 **Closing the Farm (Gold)**

[](https://www.acmicpc.net/problem/12012)

```kotlin
import java.util.*
import java.io.*

const val NONE = -1 //  어떤 정점 v가 어떤 분리 집합에도 포함되지 않을 경우 ds[v] = NONE임, closed 상태의 v의 경우 ds[v] = NONE임

class BOJ_12004 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var st: StringTokenizer

    private var n = 0   //  정점 개수
    private var m = 0   //  간선 개수

    private var dsCnt = 0   //  전체 분리 집합 개수, 1개면 현재 개방된 모든 정점들이 연결된 것임

    private lateinit var graph : Array<ArrayList<Int>>
    private lateinit var parents : IntArray      //  분리집합, parents[i] : i가 속한 분리 집합에서 i의 부모
    private lateinit var order : IntArray   //  폐쇄하는 순서, 개방하는 순서의 역순

    private val ans = Stack<String>()

    fun solve() {
        st = StringTokenizer(br.readLine())
        n = st.nextToken().toInt()
        m = st.nextToken().toInt()

        graph = Array(n + 1) { ArrayList() }
        parents = IntArray(n + 1) { NONE }
        order = IntArray(n + 1)

        repeat(m) {
            st = StringTokenizer(br.readLine())
            val v1 = st.nextToken().toInt()
            val v2 = st.nextToken().toInt()

            graph[v1].add(v2)
            graph[v2].add(v1)
        }

        repeat(n) {
            val v = br.readLine().toInt()

            order[it + 1] = v
        }

        for(i in n downTo 1) {
            val v = order[i]    //  개방할 정점

            parents[v] = v
            dsCnt++ //  일단 단독 분리집합 만들기

            for(nv in graph[v]) {   //  그래프 상에서 이웃한 정점들에 대하여
                if(parents[nv] == NONE) //  이웃한 정점이 아직 어떤 분리 집합에도 속하지 않을 경우 skip
                    continue

                if(findDS(v) != findDS(nv))   //  v와 nv가 서로 다른 분리 집합에 속할 경우
                    union(v, nv)    //  두 분리 집합 합치기
            }

            ans.push(if(dsCnt == 1) "YES\n" else "NO\n")
        }

        while(ans.isNotEmpty())
            sb.append(ans.pop())

        print(sb)
    }

    //  v가 속한 분리 집합의 우두머리 구하기
    private fun findDS(v : Int) : Int {
        if(parents[v] == NONE || parents[v] == v)   //  NONE일 경우 어떤 분리 집합에도 속하지 않음, v일 경우 v 혼자 분리 집합 이룸
            return parents[v]

        parents[v] = findDS(parents[v])
        return parents[v]
    }

    private fun union(v1 : Int, v2 : Int) {
        val v1DS = findDS(v1)
        val v2DS = findDS(v2)

        parents[v1DS] = v2DS    //  두 분리 집합이 합쳐짐
        dsCnt-- //  분리 집합 개수 1개 감소
    }
}

fun main() {
    BOJ_12004().solve()
}
```