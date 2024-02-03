# 24_02_03_daily_certification

```
[koreii] #34 데일리인증
20240203
JPA
연관관계 매핑 종류
- 다대일 vs 일대다 (다대일 쓰는 이유)

알고리즘
- 기하학 문제 풀이 (호도법, 코사인법칙)
```

# JPA

## 연관관계 매핑

### 일대다 [1:N] 단방향

연관관계의 주인이 1 쪽에 존재하는 관계

**Member Entity**

```java
@Entity
public class Member {
  @Id
  @GeneratedValue
  @Column(name = "MEMBER_ID")
  private Long id;
  @Column(name = "USERNAME")
  private String username;

  public Long getId() {
    return id;
  }

  public String getUsername() {
    return username;
  }

  public void setUsername(String username) {
    this.username = username;
  }
}
```

다 쪽이었던 Member에 @JoinColumn 어노테이션을 가지는 컬럼이 없다.

**Team Entity**

```java
@Entity
public class Team {
  @Id
  @GeneratedValue
  @Column(name = "TEAM_ID")
  private Long id;

  @Column(name = "TEAM_NAME")
  private String name;

  @OneToMany
  @JoinColumn(name = "TEAM_ID")
  private List<Member> members = new ArrayList<>();

  public Long getId() {
    return id;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public List<Member> getMembers() {
    return members;
  }
}
```

일 쪽인 Team에 FK TEAM_ID와 매핑되는 members 참조가 존재한다. 

members 참조 위에 반드시 @JoinColumn(name = “TEAM_ID”)를 추가해야 한다. 그렇지 않으면 중간 테이블인 조인 테이블 방식을 사용하여 테이블이 하나 추가된다.

**JpaMain**

```java
try {
  Member member = new Member();
  member.setUsername("member1");
  em.persist(member);

  Team team = new Team();
  team.setName("teamA");
  //  team 객체에서 MEMBER 테이블의 외래키를 관리함.
  team.getMembers().add(member);

  em.persist(team);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

**Query**

![Untitled](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/Untitled.png)

MEMBER 테이블의 update 쿼리가 추가적으로 나오면서 아래와 같이 값이 정상적으로 나온다.

![Untitled](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/Untitled%201.png)

**update 쿼리가 추가로 나오는 이유**

![Untitled](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/Untitled%202.png)

Team 객체에서 MEMBER 테이블에 존재하는 TEAM_ID에 직접 접근할 수 없어서 update 쿼리가 추가되어야 한다. 큰 차이는 아니지만 성능 상 손해가 존재한다.

Team Entity가 관리하는 FK가 TEAM 테이블에 있지 않고 MEMBER 테이블에 있어 직접 관리가 안된다.

사실 추가적인 update 쿼리보다 더 큰 문제는 따로 있다.

```java
try {
  Member member = new Member();
  member.setUsername("member1");
  em.persist(member);

  Team team = new Team();
  team.setName("teamA");
  //  team 객체에서 MEMBER 테이블의 외래키를 관리함.
  team.getMembers().add(member);

  em.persist(team);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

코드만 봐서는 왜 MEMBER 테이블의 update 쿼리가 추가적으로 나오는 지 직관적으로 알기 어렵다. 테이블이 적을 때야 상관없지만 실무에서 수많은 테이블을 관리해야 할 경우 분명 문제될 여지가 많다.

Team Entity가 관리하는 FK가 TEAM 테이블에 있지 않고 MEMBER 테이블에 있는 것 자체가 엄청난 단점이다.

**다대일 단방향 관계로 먼저 만들고, 필요할 경우 @OneToMany를 이용해 양방향 연관관계로 추가해준다.**

### 일대다 [1:N] 양방향

일대다 양방향 관계도 구현은 가능하다. 다 쪽의 참조를 읽기 전용으로 만들면 된다. 하지만 JPA 스펙 상 지원하진 않는다.

**Member Entity**

```java
@Entity
public class Member {
  @Id
  @GeneratedValue
  @Column(name = "MEMBER_ID")
  private Long id;
  @Column(name = "USERNAME")
  private String username;

  @ManyToOne
  @JoinColumn(name = "TEAM_ID", insertable = false, updatable = false)
  private Team team;

  public Long getId() {
    return id;
  }

  public String getUsername() {
    return username;
  }

  public void setUsername(String username) {
    this.username = username;
  }
}
```

**다대일 단방향 관계로 먼저 만들고, 필요할 경우 @OneToMany를 이용해 양방향 연관관계로 추가해준다.**

# Problem Solving (Algorithm & SQL)

**BOJ 1798 원뿔좌표계상의 거리**

[1798번: 원뿔좌표계상의 거리](https://www.acmicpc.net/problem/1798)

P1, P2의 각을 각각 A1, A2라고 할 때 옆면의 부채꼴에서 선분 CP1과 선분 CP2가 이루는 각을 theta라고 하면 먼저 xy 평면에서 OP1’과 OP2’이 이루는 각 theta’을 구해야 한다. (그림 참고)

![1.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/1.jpeg)

![2.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/2.jpeg)

theta’을 구하면 부채꼴에서 CP1, CP2가 이루는 각을 구할 수 있다. 밑면의 원주 중 P1’P2’의 길이와 부채꼴에서의 호 중 P1’P2’의 길이가 같음을 이용하면 된다.

![3.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/3.jpeg)

theta를 호도법으로 변환해주고 제 2 코사인 법칙을 이용하면 정답을 구할 수 있다.

![4.jpeg](24_02_03_daily_certification%2025053026a07a4866ab760f3cf845ee96/4.jpeg)

재미있는 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*
import kotlin.math.*

const val RAD = PI / 180

class BOJ_1798 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var r = 0.0
    private var h = 0.0
    private var d1 = 0.0
    private var a1 = 0.0
    private var d2 = 0.0
    private var a2 = 0.0

    private var l = 0.0 //  원뿔 빗변 길이
    private var theta = 0.0
    private var ans = 0.0

    fun solve() {
        while(true) {
            val input = br.readLine()
            if(input == null) {
                print(sb)
                break
            }

            tokens = StringTokenizer(input)
            r = tokens.nextToken().toDouble()
            h = tokens.nextToken().toDouble()
            d1 = tokens.nextToken().toDouble()
            a1 = tokens.nextToken().toDouble()
            d2  = tokens.nextToken().toDouble()
            a2 = tokens.nextToken().toDouble()

            l = sqrt(r * r + h * h)
            theta = abs(a2 - a1).coerceAtMost(abs(360 + a1 - a2))   //  60분법 중심각
            theta *= RAD //  radian
            theta = (r * theta) / l

            ans = d1 * d1 + d2 * d2 - 2 * d1 * d2 * cos(theta)
            ans = sqrt(ans)

            sb.append(String.format("%.2f\n", ans))
        }
    }
}

fun main() {
    BOJ_1798().solve()
}
```