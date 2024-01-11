# 24_01_11_daily_certification

# Spring

## Pure DI Container vs Spring Container

### Pure DI Container

```java
AppConfig appConfig = new AppConfig();
```

- 요청을 할 때마다 객체를 새로 생성하여 메모리 낭비가 심하다.
- 해당 객체가 딱 1번 생성되게 하고, 요청마다 공유하도록 설계해야 한다. → Singleton Pattern

### Singleton Pattern

```java
public class SingletonService {
  private static final SingletonService instance = new SingletonService();

  public static SingletonService getInstance() {
    return instance;
  }

  private SingletonService() {}

  public void logic() {
    System.out.println("싱글톤 객체 로직 호출");
  }
}
```

Singleton Pattern 구현, 장단점은 Java 개념서 참고

### Stateful vs Stateless

```java
public class StatefulService {
  private int price;  //  상태를 유지하는 필드
  public void order(String name, int price) {
    System.out.println("name = " + name + " price = " + price);
    this.price = price; //  여기가 문제
  }
  public int getPrice() {
    return price;
  }
}
```

```java
public class StatelessService {
  public int order(String name, int price) {
    System.out.println("name = " + name + " price = " + price);
    return price;
  }
}
```

Singleton Pattern 클래스는 Stateless하게 구현해야 한다.

### Spring Container

```java
ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
```

- 요청을 할 때마다 객체를 새로 생성하는 것이 아니라, 이미 만들어진 객체를 효율적으로 재사용한다.

## @Configuration & Singleton

```java
package inflearn.core;

import inflearn.core.discount.DiscountPolicy;
import inflearn.core.discount.FixDiscountPolicy;
import inflearn.core.discount.RateDiscountPolicy;
import inflearn.core.member.MemberRepository;
import inflearn.core.member.MemberService;
import inflearn.core.member.MemberServiceImpl;
import inflearn.core.member.MemoryMemberRepository;
import inflearn.core.order.OrderService;
import inflearn.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
    // @Bean memberService -> new MemoryMemberRepository()
    // @Bean orderService -> new MemoryMemberRepository()

    //  call AppConfig.memberService
    //  call AppConfig.memberRepository
    //  call AppConfig.memberRepository
    //  call AppConfig.orderService
    //  call AppConfig.memberRepository
    @Bean
    public MemberService memberService() {
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }

}
```

### **@Configuration Annotation 이 Spring Bean이 Singleton이 되도록 보장하는 이유**

```java
ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

AppConfig bean = ac.getBean(AppConfig.class);
//  내가 만든 AppConfig가 아님
System.out.println("bean = " + bean.getClass());
```

결과

```
bean = class inflearn.core.AppConfig$$EnhancerBySpringCGLIB$$e022469c
```

실제로 정의한 AppConfig가 쓰이는 것이 아니라 AppConfig를 상속받은 객체가 사용된다. Spring이 CGLIB이라는 바이트 코드 조작 라이브러리를 사용해 AppConfig를 상속받은 클래스를 만들어 해당 클래스를 Spring Bean으로 등록한다.

![Untitled](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/Untitled.png)

대략 다음과 같은 클래스가 생성된다.

```java
@Bean
public MemberRepository memberRepository() {
	if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) { return 스프링 컨테이너에서 찾아서 반환;
	} else { //스프링 컨테이너에 없으면
		기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록 return 반환
	}
}
```

@Bean이 붙은 메서드마다 Spring Bean이 존재하면 존재하는 Bean을 반환하고, Spring Bean이 없으면 생성해서 Spring Bean으로 등록하고 반환하는 코드가 생성된다.

따라서 Singleton을 보장할 수 있다.

@Configuration 어노테이션이 없으면 @Bean이 붙은 객체들은 Spring Bean으로 등록은 되지만 Singleton을 보장하지 않는다.

# Computer Network

## TCP Flow Control

![IMG_B404371FE253-1.jpeg](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/IMG_B404371FE253-1.jpeg)

## TCP Connection Management

[](https://chat.openai.com/share/463f5e2e-e879-47a9-babe-a9009aff1650)

### TCP 3-Way Handshake

Client와 Server의 TCP 연결을 맺기 위한 과정

![IMG_D4030722AD17-1.jpeg](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/IMG_D4030722AD17-1.jpeg)

![IMG_DB2619D36845-1.jpeg](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/IMG_DB2619D36845-1.jpeg)

### TCP 4-Way Handshake

Client와 Server의 TCP 연결을 종료하기 위한 과정

![4wayhandshake.png](24_01_11_daily_certification%203d02b8add48f4b5dac21aaff712c6508/4wayhandshake.png)

[4-Way Handshake](https://hojunking.tistory.com/107)

# Problem Solving (Algorithm & SQL)

**BOJ 19542 전단지 돌리기**

[19542번: 전단지 돌리기](https://www.acmicpc.net/problem/19542)

각 노드에서 리프 노드 사이의 거리의 최댓값을 구하고 그 값이 D 이상인 노드 개수를 구하면 된다. 이 때 루트 노드는 제외한다. DFS로 구할 수 있다.

루트 노드에서 해당 노드들을 모두 탐색하고 돌아오기 위해서 루트 노드와 이어진 간선을 두번씩 탐색해야 한다. 

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_19542 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var s = 0
    private var d = 0

    private lateinit var tree : Array<MutableList<Int>>
    private lateinit var visited : BooleanArray
    private lateinit var maxDist : IntArray         //  maxDist[i] : i 노드로부터 리프노드까지 거리 중 최댓값

    private var cnt = 0     //  어떤 리프 노드로부터 d 이상 떨어진 노드 개수 (루트 노드 제외)

    fun solve() {
        tokens = StringTokenizer(br.readLine())

        n = tokens.nextToken().toInt()
        s = tokens.nextToken().toInt()
        d = tokens.nextToken().toInt()

        tree = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)
        maxDist = IntArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()

            tree[v1].add(v2)
            tree[v2].add(v1)
        }

        dfs(s)

        for(i in 1 .. n) {
            if(maxDist[i] >= d && i != s)
                cnt++
        }

        print(2 * cnt)
    }

    private fun dfs(v : Int) {
        var max = 0 //  자식 노드들의 리프노드까지의 거리 중 최댓값
        var cnt = 0 //  자식 노드 개수

        visited[v] = true

        for(nv in tree[v]) {
            if(!visited[nv]) {
                cnt++
                dfs(nv)
                max = max.coerceAtLeast(maxDist[nv])
            }
        }

        if(cnt != 0)    //  리프노드가 아닐 경우
            maxDist[v] = max + 1
    }
}

fun main() {
    BOJ_19542().solve()
}
```