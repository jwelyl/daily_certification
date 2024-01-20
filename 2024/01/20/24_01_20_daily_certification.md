# 24_01_20_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 17501 수식 트리**

[17501번: 수식 트리](https://www.acmicpc.net/problem/17501)

수식 트리에서 연산자 종류가 +, - 밖에 없으므로 순서에 상관 없이 모든 피연산자의 최종 부호를 정한 후 더해주면 계산 결과값이 나오게 된다. 이 점을 파악하는게 어려웠다.

DFS로 루트 노드부터 시작해서 각 노드를 탐색하며, 리프 노드, 즉 피연산자일 경우 앞에 붙는 최종 부호를 붙여준다. 트리를 DFS로 탐색할 때 왼쪽 서브트리를 탐색할 때는 현재 노드의 부호에 관계 없이 서브트리의 부호대로 탐색한다. 현재 노드 부호가 -일 경우에는 오른쪽 서브트리를 탐색할 때는 서브트리 전체의 부호를 반대로 해서 탐색한다.

피연산자 앞에 붙는 + 부호와 - 부호의 개수를 알아낸 후, 피연산자들을 절대값 크기 순서대로 정렬한 후 더해준다. 더해줄 때 피연산자 값이 양수이면 + 부호 개수를 1 줄이고 그대로 더해주고, 음수이면 - 부호 값의 개수를 1 줄이고 부호를 바꾸어 더해준다. 최종적으로 + 부호 또는 - 부호 둘 중 1개만 남게 되는데 남은 수들을 부호에 따라 알맞게 더해주면 된다. 

**코드**

```java
import java.util.*
import java.io.*
import kotlin.math.abs

class BOJ_17501 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  트리 노드 개수
    private lateinit var tree : Array<Node> //  트리
    private var root = 0

    private var positive = 0    //  앞에 붙는 부호가 +인 피연산자 개수
    private var negative = 0    //  앞에 붙는 부호가 -인 피연산자 개수
    private val nums = ArrayList<Int>()  //  피연산자 리스트

    private var ans = 0

    fun solve() {
        n = br.readLine().toInt()
        root = 2 * n - 1
        tree = Array(2 * n) { Node(it) }

        for(i in 1 .. n) {
            val opr = br.readLine().toInt()
            nums.add(opr)
        }

        for(i in n + 1 ..< 2 * n) {
            tokens = StringTokenizer(br.readLine())
            val opt = tokens.nextToken()[0]
            val left = tokens.nextToken().toInt()
            val right = tokens.nextToken().toInt()

            tree[i].opt = opt == '-'
            tree[i].left = left
            tree[i].righjt = right
        }

        dfs(root, false)

        nums.sortByDescending { abs(it) }   //  절댓값이 큰 순으로 정렬

        var idx = 0
        while(positive > 0 && negative > 0) {
            if(nums[idx] >= 0) {
                ans += nums[idx]
                positive--
            }
            else {
                ans += (-nums[idx])
                negative--
            }
            idx++
        }

        while(positive > 0) {
            ans += nums[idx++]
            positive--
        }
        while(negative > 0) {
            ans += (-nums[idx++])
            negative--
        }

        println(ans)
    }

    private fun dfs(node : Int, reversed : Boolean) {
        val left = tree[node].left
        val right = tree[node].righjt

        if(left == -1 && right == -1) {   //  리프 노드일 경우
            val nowSign = if(reversed) !tree[node].opt else tree[node].opt  //  reversed가 true일 경우 부호 변경, 아닐 경우 부호 유지
            if(nowSign)
                negative++
            else
                positive++

            return
        }

        dfs(left, reversed) //  왼쪽은 그대로 탐색
        if(!tree[node].opt)  //  현재 부호가 +일 경우
            dfs(right, reversed)
        else
            dfs(right, !reversed)
    }

    private class Node(val num : Int) {
        var opt = false
        var left = -1
        var righjt = -1
    }
}

fun main() {
    BOJ_17501().solve()
}
```