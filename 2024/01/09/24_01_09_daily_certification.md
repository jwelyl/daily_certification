# 24_01_09_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 18251 내 생각에 A번인 단순 dfs 문제가 이 대회에서 E번이 되어버린 건에 관하여 (Easy)**

[18251번: 내 생각에 A번인 단순 dfs 문제가 이 대회에서 E번이 되어버린 건에 관하여 (Easy)](https://www.acmicpc.net/problem/18251)

중위 순회로 포화 이진 트리의 각 노드의 좌표를 정한다. 아래 문제와 유사한 방법으로 할 수 있다.

[2250번: 트리의 높이와 너비](https://www.acmicpc.net/problem/2250)

더 깔끔하고 간단한 구현도 있지만 어쨌든 구하면 된다.

2차원 정수 배열을 선언하고, 각 정점의 가중치를 중위 순회로 구한 좌표에 저장하면, 문제는 최대 부분 직사각형을 구하는 문제가 된다.

2차원 정수 배열의 행 크기 h는 트리의 높이로 최대 O(logN)이고, 열 크기 w는 트리의 정점 개수로 O(N)이다. 따라서 공간 복잡도는 O(hw) = O(NlogN)이 된다.

Kadene Algorithm을 이용하면 h * w 이차원 배열의 최대 부분 직사각형을 O(h^2 w)의 시간복잡도에 구할 수 있다. O(h) = O(logN)이므로 O(N(logN)^2)에 구할 수 있다.

[kadane 2D](https://nberserk.github.io/2016/11/13/kadane2d/)

이 때 모든 정점의 가중치가 음수일 경우 Kadane 알고리즘을 사용하면 2차원 배열 중 값이 0인 부분만 포함하게 되어 최댓값으로 0을 구하게 된다. 반드시 하나 이상의 정점을 포함해야 하므로 오답이다.

따라서 모든 정점이 음수일 경우 예외처리를 통해 그 중 가장 가중치가 큰 정점 하나만 포함하도록 해야 한다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.log2

class BOJ_18251 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private lateinit var tree : Array<Node>
    private var n = 0
    private var h = 0
    private var allNeg = true   //  모든 노드의 가중치가 음수일 경우 true
    private var maxValue = Long.MIN_VALUE   //  가중치 중 최댓값
    private lateinit var arr2D : Array<LongArray>

    fun solve() {
        n =  br.readLine().toInt()

        h = log2(n.toDouble() + 1).toInt()
        arr2D = Array(h) { LongArray(n) }

        tree = Array(n + 1) { Node() }
        tokens = StringTokenizer(br.readLine())

        tree[1].weight = tokens.nextToken().toInt()
        if(tree[1].weight > 0)
            allNeg = false
        maxValue = maxValue.coerceAtLeast(tree[1].weight.toLong())

        for(i in 2 .. n) {
            val parent = tree[i / 2]
            tree[i].weight = tokens.nextToken().toInt()
            if(tree[i].weight > 0)
                allNeg = false
            maxValue = maxValue.coerceAtLeast(tree[i].weight.toLong())

            if(i % 2 == 0)
                parent.left = i
            else
                parent.right = i
        }

        if(allNeg) {    //  모든 노드 가중치 음수일 경우
            println(maxValue)   //  그나마 큰 하나만 포함
            return
        }

        dfs(1,0, 0)

        println(kadane())   //  최대 부분 직사각형 구하기
    }

    private fun kadane() : Long {
        var ret = Long.MIN_VALUE

        for(i in 0 ..< h) {
            val sum = LongArray(n)
            for(j in i ..< h) {
                for(k in 0 ..< n)
                    sum[k] += arr2D[j][k]

                var curMax = sum[0]
                var max = sum[0]

                for(k in 1 ..< n) {
                    curMax = (curMax + sum[k]).coerceAtLeast(sum[k])
                    max = max.coerceAtLeast(curMax)
                }

                ret = ret.coerceAtLeast(max)
            }
        }

        return ret
    }

    private fun dfs(v : Int, yPos : Int, xPos : Int) {
        val left = tree[v].left
        val right = tree[v].right
        tree[v].y = yPos

        if(left == -1 && right == -1) { //  단말 노드일 경우
            tree[v].x = xPos
            tree[v].biggestX = xPos

            arr2D[tree[v].y][tree[v].x] = tree[v].weight.toLong()

            return
        }

        dfs(left, yPos + 1, xPos)
        tree[v].x = tree[left].biggestX + 1 //  v의 x 좌표는 v의 왼쪽 자식 서브트리에서 가장 오른쪽 좌표 + 1임
        arr2D[tree[v].y][tree[v].x] = tree[v].weight.toLong()
        dfs(right, yPos + 1, tree[v].x + 1)  //  v의 오른쪽 서브트리의 가장 왼쪽 x 좌표는 v의 x 좌표 + 1임
        tree[v].biggestX = tree[right].biggestX
    }

    private class Node {
        var y = 0
        var x = 0
        var weight = 0
        var biggestX = 0    //  Node를 루트로 하는 서브트리에 포함된 노드 중 가장 오른쪽에 있는 노드의 x 좌표

        var left = -1
        var right = -1
    }
}

fun main() {
    BOJ_18251().solve()
}
```