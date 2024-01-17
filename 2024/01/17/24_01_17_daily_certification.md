# 24_01_17_daily_certification

# Spring

## Dependency Injection 방법

- **Constructor**
- Setter
- Field
- Method

### Constructor (생성자 주입)

가장 많이 쓰이고 가장 권장되는 의존성 주입 방법

생성자 호출 시점에 딱 1번 호출된느 것이 보장된다.

불변, 필수 의존관계에 사용된다.

필드에 **final 키워드**를 사용할 수 있어, 필수적으로 초기화 및 불변성이 보장된다.

```java
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
				this.discountPolicy = discountPolicy;
    }
}
```

생성자가 하나일 경우 @Autowired 어노테이션이 없어도 자동으로 의존성이 주입된다.

### Setter (수정자 주입)

선택, 변경 가능성이 있는 의존관계에 사용된다.

생성자 주입이 없어도 추후에 의존성 주입이 가능하다. 하지만 생성자 호출 후에 값이 수정되는 것이므로 final 키워드를 사용할 수 없다.

```java
@Component
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
			this.memberRepository = memberRepository;
		}

		@Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
			this.discountPolicy = discountPolicy;
		}
}
```

### Field, Method 주입

거의 쓰이지 않는다고 봐도 무방하다. 아니, 안 쓰는게 맞다.

### Spring Container가 관리하는 Spring Bean이 의존성 자동 주입 대상

의존 관계 자동 주입은 Spring Container가 관리하는 Spring Bean이어야 동작한다.

Member와 같이 Spring Bean이 아닌 클래스의 객체는 @Autowired가 있어도 자동으로 주입되지 않는다.

## @Autowired 옵션 처리

### @Autowired(required=false)

자동 주입할 대상이 없으면 Setter 메서드 자체가 호출되지 않는다.

### @Nullable

자동 주입할 대상이 없으면 null이 입력된다.

### Optional<>

자동 주입할 대상이 없으면 Optional.empty가 입력된다.

```java
static class TestBean {
  @Autowired(required = false)
  public void setNoBean1(Member noBean1) {
    System.out.println("noBean1 = " + noBean1);
  }

  @Autowired
  public void setNoBean2(@Nullable Member noBean2) {
    System.out.println("noBean2 = " + noBean2);
  }

  @Autowired
  public void setNoBean3(Optional<Member> noBean3) {
    System.out.println("noBean3 = " + noBean3);
  }
}
```

Member가 Spring Container가 관리하는 Bean이 아니므로 자동 주입할 대상이 없다.

따라서 setNoBean1 메서드는 호출되지 않고, setNoBean2는 null을, setNoBean3은 Optional.empty이 입력된다.

## Constructor Injection을 사용해야 하는 이유

### 불변성

- 의존 관계 주입은 한번 이루어지면 의존 관계가 변경될 일이 거의 없다 불변해야 하는 경우가 대부분이다.
- setter 주입을 하려면 setter를 public으로 열어놔야 한다.
    - 누군가 실수로 변경해서는 안되는 의존성을 변경할 수 있다.
- constructor는 객체를 생성할 때 딱 한번 호출되므로 의존성을 불변하게 생성할 수 있다.

### 누락 방지

SpringFramework에서는 @Autowired가 동작할 때 의존관계가 없으면 오류가 발생해서 실행조차 안된다.

하지만 순수 Java 코드로 테스트를 수행할 경우 의존관계가 없어도 실행은 된다. 물론 필수적인 의존관계가 없으므로 NullPointerException이 발생한다.

Setter로 의존성을 주입할 경우 객체 생성 후 필수적인 의존성 주입을 누락할 소지가 있다.

Constructor로 의존성을 주입할 경우, 필수적인 의존성 주입을 누락했을 때 **컴파일 에러가 발생한다.**

### final 키워드 사용 가능

필수적인 필드의 의존성 주입이 누락될 경우 **컴파일 에러가 발생한다.**

final 키워드를 사용하면 생성자 호출 때 최초로 한번만 초기화될 수 있으므로, 생성자 주입의 경우에만 사용 가능하다.

생성자 주입을 제외한 다른 모든 의존성 주입 방법은 생성자 호출 이후에 이루어지므로 final 키워드를 사용할 수 없다.

## Lombok

### @RequiredArgsConstructor

```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
}
```

final 키워드가 붙은 필드를 모아서 생성자를 자동으로 만들어준다. 

최근 트렌드는 필수적인 필드를 초기화하는 생성자를 1개 사용하고 @Autowired를 생략하는 방법을 주로 사용한다. @RequiredArgsConstructor를 사용하면 간결한 코드로 이를 만족시킬 수 있다.


# Computer Network

## Application Layer

### Conditional GET

Web Caches (Proxy Server)에서 일관성 문제를 해결하기 위함

![1.jpeg](24_01_17_daily_certification%20cf749d24b171441ab413256144f8b61c/1.jpeg)

### DNS (Domain Name System)

전화번호부 (친구 이름 - 전화번호)

(Host Name - IP Address)

![2.jpeg](24_01_17_daily_certification%20cf749d24b171441ab413256144f8b61c/2.jpeg)

**DNS**
- Distributed Database (분산)
- Hierarchical Database (계층화)
    
    ![3.jpeg](24_01_17_daily_certification%20cf749d24b171441ab413256144f8b61c/3.jpeg)
    
- Top-Level Domain (TLD) Server
- Authoritative DNS Server
- Local DNS Name Server
- DNS Caching, TTL

### DNS Records

![4.jpeg](24_01_17_daily_certification%20cf749d24b171441ab413256144f8b61c/4.jpeg)

### DNS는 UDP를 사용

HTTP Request, Response를 주고 받기 위해 IP Address가 필요한데 IP Address를 얻기 위해, Host Name을 IP Address로 얻기 위해 DNS가 필요하다. DNS는 HTTP를 위한 사전 작업 느낌

DNS, HTTP 모두 Application Protocol이므로 Transport Layer의 서비스를 받는데 HTTP는 TCP를 사용하는 것과 달리 DNS는 UDP를 사용한다.

**DNS가 UDP를 사용하는 이유?**

- UDP는 빠르다.
- DNS에서 주고 받는 데이터는 Host Name, IP Address로 매우 작다. 유실될 확률도 작고 유실되도 타격이 적다. (HTTP는 주고 받는 데이터가 크므로 유실되면 타격이 큼)
- IP Address를 빨리 얻어와야 한다. HTTP 통신을 하기 위해서

[DNS가 UDP 사용하는 이유](https://chat.openai.com/share/52a83e51-561d-4ab5-84a3-bb3a3cacdee5)


# Problem Solving (Algorithm & SQL)

**BOJ 2642 전개도**

[2642번: 전개도](https://www.acmicpc.net/problem/2642)

전개도의 한 면에서 마주보는 면이 존재하려면 처음 인접한 면이 있는 방향으로 2번 이동했을 때 면이 존재해야 한다. 예를 들어 1번 면의 오른쪽에 다른 면이 있다면 → 방향으로 두 번 이동해서 도달할 수 있는 면이 있으면 마주보는 면이 존재한다. 이러한 면이 각 면마다 단 1개만, 모든 면에 대해 존재해야 전개도가 완성된다.

전개도를 만드는 방법을 알았으면 DFS로 잘 구현하면 된다. 방법만 알면 쉽게 풀리는 문제는 아니고 DFS 구현도 보기보다 까다로운 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2645 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val map = Array(6) { IntArray(6) }
    private val visited = Array(6) { BooleanArray(6) }
    private val posArr = Array<Pos>(7) { Pos(0, 0) }    //  각 숫자 면의 위치
    private val opposite = IntArray(7)  //  opposite[i] : i가 적인 면의 반대편 면의 숫자

    fun solve() {
        for(i in 0 ..< 6) {
            tokens = StringTokenizer(br.readLine())
            for(j in 0 ..< 6) {
                val num = tokens.nextToken().toInt()
                map[i][j] = num

                if(num in 1 .. 6)
                    posArr[num] = Pos(i, j)
            }
        }

        for(num in 1 .. 6) {
            if (!solve(num)) {
                println(0)
                return
            }

        }
        
        println(opposite[1])
    }

    private fun solve(num : Int) : Boolean {
        var dirCnt = 0  //  시작 방향으로 2번 갈 수 있는 방향 수, 1이어야 함

        val posY = posArr[num].y
        val posX = posArr[num].x

        for(initDir in 0 ..< 4) {
            for (i in 0..< 6)
                Arrays.fill(visited[i], false)
            
            if(dfs(num, posY, posX, initDir, 0))    //  num에서 initDir 방향으로 2번 가서 다른 면을 만날 수 있을 경우
                dirCnt++
        }

        return dirCnt == 1
    }

    //  num이 적힌 위치 (posY, posX)에서 initDir 방향으로 2칸 가서 도달할 수 있는 칸이 있으면 true
    private fun dfs(num : Int, posY : Int, posX : Int, initDir : Int, initDirCnt : Int) : Boolean {
        visited[posY][posX] = true

        if(initDirCnt == 2) {   //  2번만에 왔을 경우
            opposite[num] = map[posY][posX]
            return true
        }

        if(initDirCnt == 0) {
            val nPosY = posY + dy[initDir]
            val nPosX = posX + dx[initDir]

            if(!isIn(nPosY, nPosX) || map[nPosY][nPosX] == 0 || visited[nPosY][nPosX])
                return false

            return dfs(num, nPosY, nPosX, initDir, 1)
        }

        for(d in 0 ..< 4) {
            val nPosY = posY + dy[d]
            val nPosX = posX + dx[d]

            if(!isIn(nPosY, nPosX) || map[nPosY][nPosX] == 0 || visited[nPosY][nPosX])
                continue

            val add = if(initDir == d) 1 else 0
            val result = dfs(num, nPosY, nPosX, initDir, initDirCnt + add)

            if(result) 
                return true
        }

        return false
    }

    //  우 : 0, 하 : 1, 좌 : 2, 상 : 3
    private val dy = intArrayOf(0, 1, 0, -1)
    private val dx = intArrayOf(1, 0, -1, 0)

    private fun isIn(y : Int, x : Int) : Boolean {
        return y in 0 ..< 6 && x in 0 ..< 6
    }

    private class Pos(val y : Int, val x : Int)
}

fun main() {
    BOJ_2645().solve()
}
```