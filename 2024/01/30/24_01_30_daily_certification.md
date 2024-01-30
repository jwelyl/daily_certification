# 24_01_30_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 4195 친구 네트워크**

[4195번: 친구 네트워크](https://www.acmicpc.net/problem/4195)

친구 이름을 map으로 관리해서 새로운 친구가 나타날 때마다 수를 1 증가시켜서 이름에 번호를 할당하고, 이미 나타났던 친구의 경우, map에서 번호를 찾으면 된다. 찾아낸 번호들로 union-find 연산을 하면 된다.

친구 관계 수가 최대 100,000이므로 친구 수도 최대 200,000명이다. 또한 단순히 번호 별로 속한 분리 집합만 구하는 것이 아니라 해당 분리 집합에 속한 친구 수도 구해야 한다. 특정 번호가 속한 분리 집합의 친구 수를 구할 때는 특정 번호로 바로 구하는 것이 아니라 find 연산을 통해 해당 분리 집합의 우두머리 번호를 구하고, 해당 번호로 분리 집합의 크기를 구해야 실수를 없앨 수 있다. 

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 200_001 //  친구 관계 수가 최대 100,000이므로 친구 수는 최대 200,000명

class BOJ_4195 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    private val sb = StringBuilder()

    private var t = 0    //    테스트 케이스 개수
    private var f = 0    //    친구 관계 수
    private var num = 0    //    친구 번호

    private val map = HashMap<String, Int>()
    private val ds = IntArray(MAX)        //    ds[i] : i번째 친구가 속한 집합
    private val dsCnt = IntArray(MAX)     //    dsCnt[i] : i번째 친구가 속한 집합에 포함된 친구 수

    fun solve() {
        t = br.readLine().toInt()

        repeat(t) {
            map.clear()

            for(i in 0 ..< MAX) {
                ds[i] = i
                dsCnt[i] = 1
            }

            f = br.readLine().toInt()
            num = 0

            repeat(f) {
                tokens = StringTokenizer(br.readLine())

                val f1 = tokens.nextToken() //  첫번째 친구 이름
                val f2 = tokens.nextToken() //  두번째 친구 이름

                if(!map.contains(f1))   //  첫번째 친구가 처음 나올 경우
                    map[f1] = num++     //  새로운 번호 할당
                if(!map.contains(f2))   //  두번째 친구가 처음 나올 경우
                    map[f2] = num++     //  새로운 번호 할당

                var num1 = map[f1]!!    //  첫번째 친구 번호
                var num2 = map[f2]!!    //  두번째 친구 번호

                num1 = findDS(num1)     //  첫번째 친구가 속한 분리집합 우두머리
                num2 = findDS(num2)     //  두번째 친구가 속한 분리집합 우두머리

                unionDS(num1, num2) //  첫번째 친구가 속한 분리집합에 두번쨰 친구가 속한 분리집합 합치기

                sb.append("${dsCnt[num1]}\n")
            }
        }

        print(sb)
    }

    private fun findDS(v : Int) : Int {
        if(v == ds[v])
            return v

        ds[v] = findDS(ds[v])
        return ds[v]
    }

    private fun unionDS(v1 : Int, v2 : Int) {
        if(v1 != v2) {  //  두 친구가 같은 분리 집합에 속하지 않을 경우 합치기
            dsCnt[v1] += dsCnt[v2]  //  첫번째 친구의 분리집합에 두 번째 친구 분리집합 합치기
            dsCnt[v2] = dsCnt[v1]   //  두번째 친구 분리집합의 크기도 첫번째 친구 분리집합 크기
            ds[v2] = v1 //  두번째 친구의 부모가 첫번째 친구가 됨
        }
    }
}

fun main() {
    BOJ_4195().solve()
}
```