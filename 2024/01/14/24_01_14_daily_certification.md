# 24_01_14_daily_certification

# Spring

## ComponentScan

```java

@ComponentScan
public class AutoAppConfig {
  @Bean(name = "memoryMemberRepository")
  MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }
}
```

기존 설정 정보인 AppConfig처럼 Spring Bean을 수동으로 등록하지 않아도 @ComponentScan 어노테이션을 붙이면 자동으로 Spring Bean을 찾아서 등록해준다.

Component Scan의 대상이 되기 위해서는 클래스에 **@Component 어노테이션을  붙여준다.**

```java
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired  //  자동 의존관계 주입
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
		...
}
```

이전 AppConfig와 다르게 의존 관계 주입도 Component Scan의 대상이 되는 클래스 안에서 해결해야 한다.

@Autowired는 의존 관계를 자동으로 주입한다.

![IMG_E282AD5716FF-1.jpeg](24_01_14_daily_certification%2057c7aa5a94a347048ea4063334e14232/IMG_E282AD5716FF-1.jpeg)

![IMG_A13D809CD05F-1.jpeg](24_01_14_daily_certification%2057c7aa5a94a347048ea4063334e14232/IMG_A13D809CD05F-1.jpeg)

## Filter

### includeFilters

컴포넌트 스캔에 포함할 컴포넌트 (거의 사용 x)

### excludeFilters

컴포넌트 스캔에서 제외할 컴포넌트 (많이 사용 x)

## 자동 Bean 등록 vs 수동 Bean 등록

```java
@ComponentScan
public class AutoAppConfig {
  @Bean(name = "memoryMemberRepository") // 수동 Bean 등록
  MemberRepository memberRepository() {
    return new MemoryMemberRepository();
  }
}
```

```java
package inflearn.core.member;

import org.springframework.stereotype.Component;

import java.util.HashMap;
import java.util.Map;

@Component // 자동 Bean 등록
public class MemoryMemberRepository implements MemberRepository{
    private static Map<Long, Member> store = new HashMap<>();

    @Override
    public void save(Member member) {
        store.put(member.getId(), member);
    }

    @Override
    public Member findById(Long memberId) {
        return store.get(memberId);
    }
}
```

SpringFramework에서는 수동 등록한 Bean과 자동 등록한 Bean이 충돌할 경우 수동 등록한 Bean이 자동 등록한 Bean을 overriding한다.

**하지만 SpringBoot에서는 수동 등록한 Bean과 자동 등록한 Bean이 충돌할 경우, 기본적으로 오류가 발생한다.**

자동 등록한 Bean과 자동 등록한 Bean이 충돌할 경우에는 SpringFramework에서도 오류가 발생한다. 

# Problem Solving (Algorithm & SQL)

**BOJ 3482 Labyrinth**

[3482번: Labyrinth](https://www.acmicpc.net/problem/3482)

문제를 번역하면 어떤 두 빈 칸 사이에는 반드시 경로가 존재하고, 경로는 반드시 하나라고 되어 있다. 이를 통해 트리 구조임을 알 수 있다.(있어야 한다.)

두 빈 칸 사이를 잇는 가장 긴 줄의 길이를 찾는 문제이므로 트리의 지름 문제임을 알 수 있다.

기존에 해결했던 트리 지름 문제와 다른 점은 인접 리스트 형태 그래프가 아니라 격자 형태 그래프라서 root를 정하는 방식이 조금 달랐고 여러 테스트케이스를 한번에 해결하는 문제이므로 매번 테스트케이스때마다 전역변수들을 모두 초기화해줘야 했다. 그러지 않으면 ArrayOutOfBounds Exception이 발생한다. 

무작정 전역 변수를 사용하는 것의 위험성을 알 수 있었다. 

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_3482 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var t = 0
    private var row = 0
    private var col = 0
    private lateinit var map : Array<CharArray>
    private lateinit var visited : Array<BooleanArray>
    private var rootY = -1
    private var rootX = -1
    private var farthestY = 0
    private var farthestX = 0
    private var diameter = 0

    fun solve() {
        t = br.readLine().toInt()

        repeat(t) {
            tokens = StringTokenizer(br.readLine())
            col = tokens.nextToken().toInt()
            row = tokens.nextToken().toInt()

            rootY = -1
            rootX = -1
            diameter = 0

            map = Array(row) { CharArray(col) }
            visited = Array(row) { BooleanArray(col) }

            for(i in 0 ..< row) {
                map[i] = br.readLine().toCharArray()
                for(j in 0 ..< col) {
                    if(map[i][j] == '.' && rootY == -1 && rootX == -1) {
                        rootY = i
                        rootX = j
                    }
                }
            }

            if(rootY != -1 && rootX != -1) {
                dfs(rootY, rootX, 0)

                for (i in 0..< row)
                    Arrays.fill(visited[i], false)

                diameter = 0
                rootY = farthestY
                rootX = farthestX

                dfs(rootY, rootX, 0)
            }

            sb.append("Maximum rope length is $diameter.\n")
        }

        print(sb)
    }

    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< row && x in 0 ..< col
    }

    private fun dfs(y : Int, x : Int, dist : Int) {
        visited[y][x] = true

        if(dist > diameter) {
            diameter = dist
            farthestY = y
            farthestX = x
        }

        for(d in 0 ..< 4) {
            val ny = y + dy[d]
            val nx = x + dx[d]

            if(isIn(ny, nx) && map[ny][nx] == '.' && !visited[ny][nx])
                dfs(ny, nx, dist + 1)
        }
    }
}

fun main() {
    BOJ_3482().solve()
}
```