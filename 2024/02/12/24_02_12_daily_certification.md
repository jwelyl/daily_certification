# 24_02_12_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 28132 기벡을 안배운다고?**

[28132번: 기벡을 안배운다고?](https://www.acmicpc.net/problem/28132)

임의의 영벡터가 아닌 벡터 (a, b)가 있을 때, 영벡터와 수직인 벡터는 다음과 같다.

- 영벡터 (0, 0)
- 임의의 상수 k에 대해 (kb, -ka)
- 임의의 상수 k에 대해 (-kb, ka)

벡터의 크기를 1로 통일하면 상수 k를 생각할 필요 없지만, 벡터를 벡터의 크기로 나눠야 하므로 실수 연산이 불가피하다.

대신 a, b의 최대 공약수 gcd를 구해서 벡터를 나누어주면 된다.

입력받는 영벡터를 제외한 모든 벡터에 대해 벡터를 gcd로 나누어준다.

벡터를 하나씩 입력받으면서 다음을 확인한다.

1. 현재 벡터가 영벡터일 경우
    
    이전에 입력받았던 모든 벡터와 수직이므로 이전에 입력받았던 모든 벡터의 개수를 정답에 더해준다.
    
2. 현재 벡터가 영벡터가 아닐 경우
    
    gcd로 나눈 현재 벡터가 (a, b)일 때
    
    2-1. (-b, a)의 개수를 구한다.
    
    2-2. (b, -a)의 개수를 구한다.
    
    2-3. (0, 0)의 개수를 구한다.
    
    2-1, 2-2, 2-3을 구한 후 정답에 더해준다.
    

벡터를 저장하고 검색하기 위해 HashMap 자료구조를 사용한다. 주의할 점은 Vector 클래스를 정의해서 사용할 때 data class로 정의해야 한다. 그렇지 않으면 Vector 클래스의 equals와 hashCode를 직접 오버라이딩해야 한다. IDE가 사용 가능할 경우 IDE에서 제공하는 override 메서드를 그대로 사용하면 되지만, IDE를 사용하지 못하는 환경의 경우 data class를 사용하는 것이 안전할 수 있다.

**코드 (data class 사용)**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.abs

class BOJ_28132 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val vectorMap = HashMap<Vector, Int>()
    private var n = 0
    private var ans = 0L
    private val vector0 = Vector(0, 0)

    fun solve() {
        n = br.readLine().toInt()

        for(i in 1 .. n) {
            tokens = StringTokenizer(br.readLine())

            var x = tokens.nextToken().toInt()
            var y = tokens.nextToken().toInt()

            val gcd = gcd(abs(x), abs(y))
            x /= gcd
            y /= gcd

            val cur = Vector(x, y)
            val vert1 = Vector(-y, x)
            val vert2 = Vector(y, -x)   //  cur과 수직일 수 있는 벡터들 (영벡터 제외)

            if(x == 0 && y == 0)    //  현재 벡터가 영벡터일 경우
                ans += (i - 1)      //  이전 모든 벡터와 수직임
            else {
                ans += vectorMap.getOrDefault(vert1, 0)
                ans += vectorMap.getOrDefault(vert2, 0)         //  지금까지 벡터들 중 수직일 수 있는 영벡터가 아닌 벡터 개수 더하기
                ans += vectorMap.getOrDefault(vector0, 0)       //  영벡터 개수 더하기
            }
            vectorMap[cur] = vectorMap.getOrDefault(cur, 0) + 1 //  현재 벡터 추가
        }

        print(ans)
    }

    private fun gcd(num1 : Int, num2 : Int) : Int {
        if(num1 == 0 && num2 == 0) return Int.MAX_VALUE
        if(num1 == 0) return num2
        if(num2 == 0) return num1

        var n1 = num1.coerceAtLeast(num2)
        var n2 = num2.coerceAtMost(num1)

        while(true) {
            val r = n1 % n2

            if(r == 0)
                break

            n1 = n2
            n2 = r
        }

        return n2
    }

    private data class Vector(val x : Int, val y : Int)
}

fun main() {
    BOJ_28132().solve()
}
```

**코드 (equals, hashCode 직접 오버라이딩)**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.abs

class BOJ_28132 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val vectorMap = HashMap<Vector, Int>()
    private var n = 0
    private var ans = 0L
    private val vector0 = Vector(0, 0)

    fun solve() {
        n = br.readLine().toInt()

        for(i in 1 .. n) {
            tokens = StringTokenizer(br.readLine())

            var x = tokens.nextToken().toInt()
            var y = tokens.nextToken().toInt()

            val gcd = gcd(abs(x), abs(y))
            x /= gcd
            y /= gcd

            val cur = Vector(x, y)
            val vert1 = Vector(-y, x)
            val vert2 = Vector(y, -x)   //  cur과 수직일 수 있는 벡터들 (영벡터 제외)

            if(x == 0 && y == 0)    //  현재 벡터가 영벡터일 경우
                ans += (i - 1)      //  이전 모든 벡터와 수직임
            else {
                ans += vectorMap.getOrDefault(vert1, 0)
                ans += vectorMap.getOrDefault(vert2, 0)         //  지금까지 벡터들 중 수직일 수 있는 영벡터가 아닌 벡터 개수 더하기
                ans += vectorMap.getOrDefault(vector0, 0)       //  영벡터 개수 더하기
            }
            vectorMap[cur] = vectorMap.getOrDefault(cur, 0) + 1 //  현재 벡터 추가
        }

        print(ans)
    }

    private fun gcd(num1 : Int, num2 : Int) : Int {
        if(num1 == 0 && num2 == 0) return Int.MAX_VALUE
        if(num1 == 0) return num2
        if(num2 == 0) return num1

        var n1 = num1.coerceAtLeast(num2)
        var n2 = num2.coerceAtMost(num1)

        while(true) {
            val r = n1 % n2

            if(r == 0)
                break

            n1 = n2
            n2 = r
        }

        return n2
    }

    private class Vector(val x : Int, val y : Int) {
        override fun equals(other: Any?): Boolean {
            if (this === other) return true
            if (javaClass != other?.javaClass) return false

            other as Vector

            if (x != other.x) return false
            if (y != other.y) return false

            return true
        }

        override fun hashCode(): Int {
            var result = x
            result = 31 * result + y
            return result
        }
    }
}

fun main() {
    BOJ_28132().solve()
}
```

만약 직접 오버라이딩해야 할 경우 위처럼 하면 된다.