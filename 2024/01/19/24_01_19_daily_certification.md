# 24_01_19_daily_certification

# Spring

## 조회된 Bean이 2개 이상일 때

@Autowired는 타입으로 Bean을 조회하므로, 부모 타입을 상속받는 타입의 Bean이 여러 개일 경우 모두 조회된다. 이 경우 **NoUniqueBeanDefinitionException** 오류가 발생한다.

@Autowired에 직접 하위 타입 Bean을 지정하면 오류 자체는 해결할 수 있다. 하지만 하위 타입으로 지정하는 것은 DIP(의존 역전 원칙, 상위 타입을 참조하라는 원칙)에 위배되어 유연성이 떨어진다.

## 해결법

### @Autowired 필드명, 파라미터명 매칭

기존 코드

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

기존 코드에서는 DiscountPolicy를 상속받는 fixDiscountPolicy, rateDiscountPolicy 2개의 Bean이 조회되어 **NoUniqueBeanDefinitionException 오류가 발생했다.**

@Autowired 필드명 매칭 코드

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy rateDiscountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

먼저 Bean 타입으로 조회하고, 2개 이상의 Bean이 조회될 경우 필드명, 파라미터명을 매칭하여 rateDiscountPolicy와 매칭된다.

### @Qualifier 사용

@Qualifier 어노테이션을 통해 추가 구분자를 붙여줘서 이를 이용해 Bean을 매칭시킨다. Bean 이름 자체는 그대로 유지된다.

먼저 Bean으로 등록할 클래스에 @Qualifier를 붙여준다.

```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
@Qualifier("fixDiscountPolicy")
public class FixDiscountPolicy implements DiscountPolicy {}
```

Bean 주입 시 @Qualifier를 붙여주고 등록한 이름을 적어준다.

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainDiscountPolicy") DiscountPolicy rateDiscountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

만약 @Qualifier("mainDiscountPolicy")를 찾지 못하면 mainDiscountPolicy라는 Bean을 추가로 찾는다. 그마저도 찾지 못하면 NoSuchBeanDefinitionException이 발생한다.

### @Primary 사용

@Autowired 시에 여러 Bean이 매칭되면 @Primary가 우선권을 가진다.

```java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```

RateDiscountPolicy에 @Primary 어노테이션이 붙었으므로

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy rateDiscountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

rateDiscountPolicy가 우선적으로 주입된다.

@Qualifier와 비교했을 때, @Qualifier를 사용하면 Bean 등록과 주입되는 코드에 모두 @Qualifier를 붙여주어야 해서 유지보수가 어렵지만, @Primary를 사용하면 등록되는 Bean에만 @Primary를 붙여주면 된다.

@Primary는 기본값처럼 동작하고, @Qualifier는 상세하게 동작한다. 따라서 **@Qualifier가 우선권이 높다.**

## Annotation 직접 만들기

@Qualifer를 사용할 경우, 문자열로 적기 때문에 컴파일시 타입 체크가 안된다.

```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
@Qualifier("fixDiscountPolicy")
public class FixDiscountPolicy implements DiscountPolicy {}
```

Bean 주입 시 @Qualifier를 붙여주고 등록한 이름을 잘못 적을 시 컴파일러는 발견하지 못한다.

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainnDiscountPolicy") DiscountPolicy rateDiscountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

컴파일러가 이런 오류를 발견하지 못하는 문제를 Annotation을 직접 만들어 해결할 수 있다.

cf) @Qualifier Annotation

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Qualifier {

	String value() default "";

}
```

@Qualifier Annotation의 Annotation을 따와 커스텀 Annotation MainDiscountPolicy Annotation을 만든다.

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {
}
```

사용할 때는 등록할 Bean에 @MainDiscountPolicy Annotation을 적어주고, 

```java
@Component
@MainDiscountPolicy
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
@Qualifier("fixDiscountPolicy")
public class FixDiscountPolicy implements DiscountPolicy {}
```

Bean을 주입할 때 추가해주면 된다.

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy rateDiscountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}
```

## 조회된 2개 이상의 Bean을 동적으로 모두 사용할 때

예를 들어 클라이언트가 할인의 종류(fixDiscountPolicy, rateDiscountPolicy)를 동적으로 선택 가능하게 구현하고 싶을 경우

```java
static class DiscountService {
  private final Map<String, DiscountPolicy> policyMap;
  private final List<DiscountPolicy> policies;

  public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
    this.policyMap = policyMap;
    this.policies = policies;
    System.out.println("policyMap = " + policyMap);
    System.out.println("policies = " + policies);
  }

  public int discount(Member member, int price, String discountCode) {
    DiscountPolicy discountPolicy = policyMap.get(discountCode);
    return discountPolicy.discount(member, price);
  }
}
```

DiscountService는 Map으로 모든 DiscountPolicy를 주입받는다. key 값으로 Bean 이름, value 값으로 Bean 객체로 주입되므로 이를 이용해 동적으로 Spring Bean을 찾아서 실행할 수 있다.

```java
ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);
```

AnnotationConfigApplicationContext 생성자로 Spring Container에 AutoAppConfig와 DiscountService를 Bean으로 등록하면, AutoAppConfig에서 ComponentScan으로 DiscountPolicy의 하위 타입 Bean을 모두 등록하고, 이를 이용해 DiscountService Bean에 의존성을 주입할 수 있다.
<br/>
<br/>
<br/>
# Problem Solving (Algorithm & SQL)

**BOJ 14657 준오는 최종인재야!!**

[14657번: 준오는 최종인재야!!](https://www.acmicpc.net/problem/14657)

트리의 각 간선의 길이를 1로 봤을 때 임의의 두 정점 사이의 거리가 최대인 것은 두 정점 사이 경로에 속한 정점의 개수가 가장 많은 것으로 생각할 수 있다. 모든 간선의 길이를 1로 간주하여 트리의 지름을 구하고 그 지름의 실제 길이(주어진 간선 길이를 이용)를 구하면 된다.

트리의 지름이 여러 개일 수 있다. 지름이 여러 개일 경우, 지름의 실제 길이가 가장 작은 지름을 구하면 된다.

일반적인 트리의 지름 구하기 문제처럼 루트를 기준으로 간선의 길이를 1로 생각했을때 최대 간선이 되는 정점을 DFS로 찾고, 그 정점을 다시 루트로 해서 DFS로 다시 지름을 찾으면 된다. 다만 간선 길이를 1로 가정하고 구한 지름 개수가 여러 개일 경우, 실제 간선 길이를 고려해서 실제 지름 길이가 가장 짧은 것을 구하면 된다.

간선 1로 했을 때 길이와 실제 간선 길이를 모두 고려해야 함에도 기존 트리의 지름 구하기 방법과 같은 풀이가 가능한 이유는 정확히 모르겠다. 여러 지름이 존재할 경우에도 성립하는 이유를 확인해야 한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_14657 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private var t = 0

    private lateinit var tree : Array<MutableList<Node>>
    private lateinit var visited : BooleanArray
    private var root = 1
    private var diameter = 0
    private var farthest = 0
    private var minTime = Int.MAX_VALUE

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        t = tokens.nextToken().toInt()

        tree = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val cost = tokens.nextToken().toInt()

            tree[v1].add(Node(v2, cost))
            tree[v2].add(Node(v1, cost))
        }

        dfs(root, 0, 0)
        root = farthest
        diameter = 0
        minTime = Int.MAX_VALUE
        Arrays.fill(visited, false)
        dfs(root, 0, 0)

        println(if(minTime % t == 0) minTime / t else minTime / t + 1)
    }

    private fun dfs(v : Int, dist : Int, time : Int) {
        visited[v] = true

        if(dist > diameter) {
            diameter = dist
            farthest = v
            minTime = time
        } else if(dist == diameter && minTime > time) {
            farthest = v
            minTime = time
        }

        for(node in tree[v]) {
            val nv = node.v
            val ndist = dist + 1
            val ntime = time + node.time

            if(!visited[nv])
                dfs(nv, ndist, ntime)
        }
    }

    private class Node(val v : Int, val time : Int)
}

fun main() {
    BOJ_14657().solve()
}
```