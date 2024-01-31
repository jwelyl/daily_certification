# 24_01_31_daily_certification

# JPA

## 연관관계 매핑

### **연관관계가 없는 객체 (Team 참조 대신 Team의 PK를 FK로 그대로 가짐)**

![Untitled](24_01_31_daily_certification%20a93d6959fb274f75aefdfc71b5bfc6eb/Untitled.png)

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

  @Column(name = "TEAM_ID")
  private Long teamId;

  public Long getId() {
    return id;
  }

  public String getUsername() {
    return username;
  }

  public void setUsername(String username) {
    this.username = username;
  }

  public Long getTeamId() {
    return teamId;
  }

  public void setTeamId(Long teamId) {
    this.teamId = teamId;
  }
}
```

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

  public Long getId() {
    return id;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }
}
```

**DB Schema**

![Untitled](24_01_31_daily_certification%20a93d6959fb274f75aefdfc71b5bfc6eb/Untitled%201.png)

Member 테이블이 Team 테이블의 PK 값을 그대로 가지고 있다.

**JpaMain**

```java
try {
  Team team = new Team();
  team.setName("TeamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setTeamId(team.getId());
  em.persist(member);

	Member findMember = em.find(Member.class, member.getId());

  Long findTeamId = findMember.getTeamId();
  Team findTeam = em.find(Team.class, findTeamId);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

**Query**

![Untitled](24_01_31_daily_certification%20a93d6959fb274f75aefdfc71b5bfc6eb/Untitled%202.png)

member의 TEAM_ID를 설정하기 위해서 team에서 ID를 가져와야 한다. 또한 member가 속한 team을 찾기 위해서 member의 FK인 TEAM_ID를 이용해 team을 찾아야 한다. 

### 단방향 연관관계

**객체 연관관계 사용**

![Untitled](24_01_31_daily_certification%20a93d6959fb274f75aefdfc71b5bfc6eb/Untitled%203.png)

MEMBER 테이블이 TEAM 테이블의 PK를 FK로 가지므로 MEMBER : TEAM은 다대일(N : 1) 관계이다.

Member 객체가 TEAM 테이블의 PK를 FK로 직접 가지는 대신 Team 객체의 참조를 가진다. 

![Untitled](24_01_31_daily_certification%20a93d6959fb274f75aefdfc71b5bfc6eb/Untitled%204.png)

**즉 FK와 참조가 매핑된다.**

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

  public Team getTeam() {
    return team;
  }

  public void setTeam(Team team) {
    this.team = team;
  }
}
```

Member 객체는 Team 객체의 참조 team를 가지게 된다. 이 때 MEMBER 테이블과 TEAM 테이블은 N : 1 관계이므로 **@ManyToOne** 어노테이션을 붙여줘야 하며, team 참조가 MEMBER 테이블의 FK TEAM_ID와 매핑되어야 하므로 **@JoinColumn(name = “TEAM_ID”)** 어노테이션을 붙여준다.

**DB Schema**

![Untitled](24_01_31_daily_certification%20a93d6959fb274f75aefdfc71b5bfc6eb/Untitled%205.png)

Member 테이블이 FK로 TEAM_ID를 가지는 것을 알 수 있다. 즉 team 참조가 FK로 잘 매핑되었다.

**JpaMain**

```java
try {
  Team team = new Team();
  team.setName("TeamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setTeam(team);
  em.persist(member);

  Member findMember = em.find(Member.class, member.getId());
  Team findTeam = findMember.getTeam();
  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

보다 객체지향적으로 연관관계를 맺어줄 수 있고 FK 없이도 조회가 가능하다.

cf) find할 때 select 쿼리가 나오지 않는 이유

team, member는 이미 Persistence Context에 들어가 있으므로 DB가 아니라 1차 캐시에서 바로 가져올 수 있기 때문dp select 쿼리가 나오지 않는다.

select 쿼리를 보고 싶으면

```java
try {
  Team team = new Team();
  team.setName("TeamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setTeam(team);
  em.persist(member);

  em.flush();
  em.clear();

  Member findMember = em.find(Member.class, member.getId());
  Team findTeam = findMember.getTeam();
  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

flush를 통해, Persistence Context에 있는 insert 쿼리를 DB로 보내 저장하고(싱크를 맞춘다.), clear로 Persistence Context를 초기화하면, DB에서 member, team을 찾아야 하므로 select 쿼리가 나온다. 

![Untitled](24_01_31_daily_certification%20a93d6959fb274f75aefdfc71b5bfc6eb/Untitled%206.png)

# Problem Solving (Algorithm & SQL)

**BOJ 1208 부분수열의 합 2**

[1208번: 부분수열의 합 2](https://www.acmicpc.net/problem/1208)

정수의 개수가 최대 40개이므로 완전탐색으로 모든 경우를 따지려면 2^40개의 경우를 모두 따져야 하므로 시간초과를 피할 수 없다.

정수를 20개씩 나누어 각각의 부분집합을 모두 구하면 2^20개의 경우가 있다. 오른쪽 20개의 부분집합의 합을 구하고 왼쪽 20개의 부분집합 합과 비교하여 둘의 합이 S가 되는 경우의 수를 더해주면 된다. 경우의 수는 이분탐색을 응용해 구할 수 있다.

주의할 점으로 부분 수열의 크기가 양수이므로 반드시 1개 이상의 원소를 골라야 한다. 따라서 다음과 같이 세 가지 경우가 존재한다.

1. 왼쪽에서만 1개 이상 골라서 S를 만드는 경우
2. 오른쪽에서만 1개 이상 골라서 S를 만드는 경우
3. 왼쪽, 오른쪽에서 적어도 각각 1개 이상씩 골라서 S를 만드는 경우

또한 경우의 수가 Integer를 넘어갈 수 있음에 유의해야 한다. 완전탐색으로 가능한 경우의 수가 2^40이므로 이를 짐작할 수 있어야 한다.

**코드**

```kotlin
import java.io.*
import java.util.*

class BOJ_1208 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private lateinit var nums : IntArray
    private val leftSum = ArrayList<Int>()
    private val rightSum = ArrayList<Int>()

    private var n = 0
    private var s = 0
    private var ans = 0L

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        s = tokens.nextToken().toInt()
        nums = IntArray(n)

        tokens = StringTokenizer(br.readLine())
        for(i in 0 ..< n)
            nums[i] = tokens.nextToken().toInt()

        subset(0, 0, (n - 1) / 2, 0, leftSum, 0)
        subset((n + 1) / 2, (n + 1) / 2, n - 1, 0, rightSum, 0)

        leftSum.sort()

        //    1. 오른쪽에서는 하나도 안 뽑은 경우
        ans += binarySearch(s)    //    왼쪽에서 1개 이상을 뽑아 s를 만들기
        //    2. 오른쪽에서 1개 이상 뽑은 경우
        for(num in rightSum) {
            if(num == s)    //    오른쪽에서 뽑은 것만으로도 s를 만들 수 있을 경우
                ans++

            ans += binarySearch(s - num)    //    왼쪽에서 s - num 개수 찾아서 더하기
        }

        println(ans)
    }

    private fun subset(nth : Int, from : Int, to : Int, sum : Int, sumList : ArrayList<Int>, cnt : Int) {
        if(nth == to + 1) {
            if(cnt > 0)    //    1개 이상 뽑은 경우
                sumList.add(sum)
            return
        }

        //    nth번째 수 포함 x
        subset(nth + 1, from, to, sum, sumList, cnt)
        //    nth번째 수 포함 o
        subset(nth + 1, from, to, sum + nums[nth], sumList, cnt + 1)
    }

    //    leftSum에서 target의 개수 찾기
    private fun binarySearch(target : Int) : Int {
        var start = 0
        var end = leftSum.size - 1
        var leftBound = -1
        var rightBound = -1

        //    leftBound 찾기
        while(start <= end) {
            val mid = (start + end) / 2

            if(leftSum[mid] == target) {    //    mid 값이 같을 경우
                leftBound = mid    //    일단 저장
                end = mid - 1    //    더 왼쪽에서 찾아보기
            }
            else if(leftSum[mid] < target)    //    mid 값이 더 작을 경우
                start = mid + 1    //    더 오른쪽에서 찾아보기
            else    //    mid 값이 더 클 경우
                end = mid - 1    //    더 왼쪽에서 찾아보기
        }

        if(leftBound == -1)
            return 0

        start = 0
        end = leftSum.size - 1

        //    rightBound 찾기
        while(start <= end) {
            val mid = (start + end) / 2

            if(leftSum[mid] == target) {    //    mid 값이 같을 경우
                rightBound = mid    //    일단 저장
                start = mid + 1    //    더 오른쪽쪽에서 찾아보기
            }
            else if(leftSum[mid] < target)    //    mid 값이 더 작을 경우
                start = mid + 1    //    더 오른쪽에서 찾아보기
            else    //    mid 값이 더 클 경우
                end = mid - 1    //    더 왼쪽에서 찾아보기
        }

        return rightBound - leftBound + 1
    }
}

fun main() {
    BOJ_1208().solve()
}
```