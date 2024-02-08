# 24_02_08_daily_certification

# Computer Network

## Network Layer

### Hierarchical Routing

![hierarchical_routing.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/hierarchical_routing.jpeg)

### Autonomous Systems (ASes)

고유 번호(Autonomous System Number, ASN) 존재

### Intra-AS Routing Algorithms

네트워크 내부 라우터 간의 라우팅 알고리즘 (AS가 선택함)

목적이 분명함 (최소 cost)

- Link State Algorithm → OSPF
- Distance Vector Algorithm → RIP

### Inter-AS Routing Algorithms

네트워크 사이의 라우팅 알고리즘 (결정 주체가 모호함)

목적이 불분명함 (최소 cost 불가. 한국에서 베이징으로 최소 cost로 가고 싶다고 북한 거칠 순 없음)

정치, 사회, 경제적 논리가 작용함

- BGP

![customervsprovider.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/customervsprovider.jpeg)

AS라고 다 같은 AS가 아니다. 예를 들어 SKT 같은 큰 AS도 있고, 서강대학교 같은 작은 AS도 있다. (갑을 관계?)

Provider가 Customer에게 Internet Access를 제공하고 Customer는 Provider에게 비용을 제공한다.

![peerpeer.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/peerpeer.jpeg)

하지만 SKT와 KT라면? 실제로 누가 더 잘나가고를 떠나서 비슷한 급이다. Peer 관계에서는 비용을 주고 받는 것이 없다.

검은 점선의 traffic의 경우 가운데 AS는 비용을 받지도 않으면서 Internet Access만 제공하므로 전혀 이득이 없다. 이런 traffic은 허용되지 않는다.

Customer, Provider, Peer 모두 상대적이다. SKT가 서강대 상대로는 Provider이지만 AT&T 상대로는 Customer일 수 있다.

### Border Gateway Protocol (BGP)

Network들의 Gateway Router 간의 Routing Protocol이다.

Link State Algorithm, Distance Vector Algorithm과 같은 최소 비용 목적이 아니라 정책에 따라 좌우된다.

![bgp.jpeg](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/bgp.jpeg)

# JPA

## 즉시 로딩 vs 지연 로딩

### 문제 제기

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled.png)

Member가 Team의 참조자를 가지고 있고, MEMBER 테이블에 TEAM 테이블의 FK를 가지고 있는 상황이라고 가정해보자.

**JpaMain**

```java
try {
  Member member = em.find(Member.class, 1L);
  printMemberAndTeam(member); // Member와 Member가 속한 Team 정보 모두 출력

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}

emf.close();
```

Member 정보와 Team 정보를 동시에 출력해야 하므로 MEMBER와 TEAM을 모두 조회하는 쿼리가 나오면 효율적일 것이다.

**JpaMain**

```java
try {
  Member member = em.find(Member.class, 1L);
  printMember(member); // Member 정보만 출력

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}

emf.close();
```

하지만 Member 정보만 필요로 한다면 필요도 없는 Team 정보를 가져오기 위해 JOIN을 통해 TEAM 테이블까지 조회하는 것은 손해이다.

이러한 비효율성을 해결하는 것이 지연 로딩이다.

### 지연 로딩 LAZY

```java
@Entity
public class Member extends BaseEntity {
  @Id
  @GeneratedValue
  @Column(name = "MEMBER_ID")
  private Long id;
  @Column(name = "USERNAME")
  private String username;

  @ManyToOne(fetch = FetchType.LAZY) /* 지연 로딩 */
  @JoinColumn(name = "TEAM_ID")
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

  public void setId(Long id) {
    this.id = id;
  }

  public Team getTeam() {
    return team;
  }

  public void setTeam(Team team) {
    this.team = team;
  }
}
```

Team 참조자의 @ManyToOne 어노테이션의 fetch 값을 **FetchType.LAZY**로 변경하자.

(기본값은 FetchType.EAGER)

이렇게 하면 Team 객체 team을 Proxy로 조회한다.

**JpaMain**

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  em.flush();
  em.clear();

  Member m = em.find(Member.class, member1.getId());

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%201.png)

Team에 관한 정보는 조회하지 않고 Member에 대한 정보만 조회한다.

```java
try {
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member1 = new Member();
  member1.setUsername("member1");
  member1.setTeam(team);

  em.persist(member1);

  em.flush();
  em.clear();

  Member m = em.find(Member.class, member1.getId());
  System.out.println("m.team = " + m.getTeam().getClass());

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

추가로 getTeam()으로 team을 얻어와 class를 확인하면 team은 Proxy로 가져온 것을 알 수 있다.

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%202.png)

```java
try {
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member1 = new Member();
  member1.setUsername("member1");
  member1.setTeam(team);

  em.persist(member1);

  em.flush();
  em.clear();

  Member m = em.find(Member.class, member1.getId());
  System.out.println("m.team = " + m.getTeam().getClass());

  System.out.println("============================================");
  System.out.println("m.team.name = " + m.getTeam().getName());
  System.out.println("============================================");

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

team에 관한 정보를 조회하자 그제서야 DB를 조회하여 Proxy 객체를 초기화한다.

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%203.png)

### 지연 로딩 LAZY

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%204.png)

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%205.png)

team 자체를 가져올 때는 Proxy다. (m.getTeam() 해봤자 Proxy임)

team의 특정 Property를 사용하면 그 때 Proxy가 초기화된다.

### 지연 로딩 LAZY vs 즉시 로딩 EAGER

Member는 자주 사용하지만 Member가 속한 Team을 조회할 일이 많지 않다면 지연 로딩을 사용하는게 효율적이다. 하지만 Team도 Member 못지 않게 조회할 일이 많다면 지연 로딩으로 Member 조회 쿼리와 Team 조회 쿼리가 2번씩 나가는 것은 비효율적이다.

이럴 경우 즉시 로딩을 사용하는게 바람직하다. 아니다. 그래도 지연 로딩을 사용하는게 좋다. 특히 실무에선

```java
@Entity
public class Member extends BaseEntity {
  @Id
  @GeneratedValue
  @Column(name = "MEMBER_ID")
  private Long id;
  @Column(name = "USERNAME")
  private String username;

  @ManyToOne(fetch = FetchType.EAGER) /* 즉시 로딩 */
  @JoinColumn(name = "TEAM_ID")
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

  public void setId(Long id) {
    this.id = id;
  }

  public Team getTeam() {
    return team;
  }

  public void setTeam(Team team) {
    this.team = team;
  }
}
```

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%206.png)

애초에 Member m1을 찾을 때 Team과 JOIN한 쿼리 한 번 나오며 team은 Proxy가 아니라 원본 객체를 가져온다.

### 즉시 로딩

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%207.png)

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%208.png)

### 중요!! 주의 사항

- **가급적 지연 로딩만 사용(특히 실무에서)**
- 즉시 로딩을 적용하면 예상하지 못한 SQL이 발생
- **즉시 로딩은 JPQL에서 N+1 문제를 일으킨다.**
- **@ManyToOne, @OneToOne은 기본이 즉시 로딩 → LAZY로 설정**
- @OneToMany, @ManyToMany는 기본이 지연 로딩

**즉시 로딩은 JPQL에서 N+1 문제를 일으킨다.** 이게 무슨 말인가? 예시를 보자.

```java
try {
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member1 = new Member();
  member1.setUsername("member1");
  member1.setTeam(team);

  em.persist(member1);

  em.flush();
  em.clear();

  //  JPQL 쿼리
  List<Member> members = em.createQuery("select m from Member m", Member.class).getResultList();

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%209.png)

분명 EAGER로 했는데도 select Member 쿼리와 select Team 쿼리 2개의 쿼리가 나간다. 왜 그럴까?

```java
//  JPQL 쿼리
List<Member> members = em.createQuery("select m from Member m", Member.class).getResultList();
```

이 JPQL을 SQL로 번역하면

```sql
SELECT * FROM MEMBER;
```

인데 이렇게 가져온 Member에 Team이 즉시 로딩이다. 따라서 Team을 또 가져와야 한다.

```sql
SELECT * FROM TEAM WHERE TEAM_ID = xxx;
```

더 끔찍한 예제를 보자.

```java
try {
  Team teamA = new Team();
  teamA.setName("teamA");
  em.persist(teamA);

  Member member1 = new Member();
  member1.setUsername("member1");
  member1.setTeam(teamA);

  em.persist(member1);

  Team teamB = new Team();
  teamB.setName("teamB");
  em.persist(teamB);

  Member member2 = new Member();
  member2.setUsername("member2");
  member2.setTeam(teamB);

  em.persist(member2);

  em.flush();
  em.clear();

  //  JPQL 쿼리
  List<Member> members = em.createQuery("select m from Member m", Member.class).getResultList();

  //  SQL : SELECT * FROM MEMBER;
  //  SQL : SELECT * FROM TEAM WHERE TEAM_ID = xxx;

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%2010.png)

member1, member2는 SELECT MEMBER로 한번에 가져온다. 문제는 member1, member2가 서로 다른 team teamA, teamB를 가지고 있어서 SELECT TEAM 쿼리가 각각 1번씩 2번 나간다.
N+1에서 N이 2인 경우이다.

만약 member가 100만개, 각각의 team도 100만개면 member 조회 쿼리 1번, 각각의 team 조회 쿼리가 100만번 나가게 된다. Wow!! (100만 + 1)

지연 로딩을 할 경우 TEAM 조회 쿼리는 나가지 않는다.

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%2011.png)

**N + 1 문제 해결 방법**

1. **일단 모든 연관 관계를 지연 로딩으로 설정한다.**
2. Fetch Join (JPQL에서 자세히 다룰 예정)
3. Batch Size (1 + 1)

**Fetch Join**

대부분 1, 2를 조합해서 해결한다.

```java
//  JPQL 쿼리
List<Member> members = em.createQuery("select m from Member m join fetch m.team", Member.class).getResultList();
```

![Untitled](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/Untitled%2012.png)

JOIN으로 team을 다 채워서 나오므로 쿼리는 1번만 나간다.

**@ManyToOne, @OneToOne은 반드시 LAZY로 직접 변경해줘야 한다.**

**결론은 일단 모든 연관관계를 지연 로딩으로 처리해두자.**

# Problem Solving (Algorithm & SQL)

**BOJ 13144 List of Unique Numbers**

[13144번: List of Unique Numbers](https://www.acmicpc.net/problem/13144)

풀이는 그림으로 대체한다. 어차피 나 혼자 볼거니까 ㅎㅎ

![2P1.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P1.png)

![2P2.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P2.png)

![2P3.png](24_02_08_daily_certification%200d6f5a20fea04dbab798abbaae793165/2P3.png)

중복이 발생할 때까지 end 포인터를 이동시키면서 end 포인터로 끝나는 부분 수열들을 더해준다.

중복이 발생할 경우 중복이 사라질 때까지 start 포인터를 이동시킨다.

중복 체크는 visited 배열을 사용했다. 가능한 수가 최대 100,000이므로 가능했는데 만약 더 큰 수의 중복 체크가 필요하다면 HashSet을 사용할 수 있을 것 같다.

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 100_001

class BOJ_13144 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var sequence : IntArray
    private val visited = BooleanArray(MAX)

    private var ans = 0L

    fun solve() {
        n = br.readLine().toInt()
        sequence = IntArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            sequence[i] = tokens.nextToken().toInt()

        var start = 0
        var end = 0

        while(end < n) {
            while(visited[sequence[end]]) {
                visited[sequence[start]] = false
                start++
            }

            while(end < n && !visited[sequence[end]]) {
                ans += (end - start + 1)
                visited[sequence[end]] = true
                end++
            }
        }

        println(ans)
    }
}

fun main() {
    BOJ_13144().solve()
}
```
