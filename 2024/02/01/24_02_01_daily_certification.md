# 24_02_01_daily_certification

# JPA

## 연관관계 매핑

### 양방향 연관관계 & 연관관계의 주인

**단방향 연관관계**

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled.png)

테이블에서는 Foreign Key TEAM_ID로 JOIN을 통해 MEMBER에서 TEAM으로, TEAM에서 MEMBER로 접근이 가능하다. **즉 테이블은 FK 만으로 양방향 참조가 가능하다.**

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%201.png)

하지만 객체에서는 Member에서 참조자를 통해 Team에 접근이 가능하지만 Team에서는 Member에 접근이 불가능하다.

![스크린샷, 2024-02-01 22.24.51.jpeg](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/abc.jpeg)

객체에서도 양방향 참조가 가능하기 위해서는 Team에서도 Member를 참조하는 참조가 필요하다.

Member와 Team이 N : 1 관계이므로 **Member에서 Team은 다대일(@ManyToOne), Team에서 Member는 일대다(@OneToMany)이다.**

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

  @OneToMany(mappedBy = "team") //  Member의 team에 mapping
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
}
```

@OneToMany 어노테이션으로 Team에서 Member를 일대다로 참조하고 있음을 나타내고 mappedBy로 Member의 team 참조와 mapping시킨다.

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

  em.flush();
  em.clear();
  
  Member findMember = em.find(Member.class, member.getId());
  List<Member> members = findMember.getTeam().getMembers();

  for(Member m : members) {
    System.out.println("m = " + m.getUsername());
  }
  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

**Member에서 Team으로, Team에서 Member로 양방향 참조가 가능하다.**

### 객체 vs 테이블 관계의 차이

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%202.png)

객체 : Member → Team의 참조, Team → Member의 참조 (단방향 연관관계 2개, 양방향)

테이블 : FK로 Member ↔ Team 참조 가능 (양방향 연관관계 1개, 방향이 없다고도 함)

양방향 연관관계에서 Member 객체도 Team의 참조를 가지고, Team 객체도 Member의 참조를 가진다. 이 때 **어떤 객체의 참조를  테이블의 FK와 매핑시켜야 할까?**

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%203.png)

둘 중 하나로 FK를 관리해야 한다. **관리하는 참조를 연관관계의 주인이라고 한다.**

### 연관관계의 주인 (Owner)

**양방향 매핑에서 FK를 관리하는 참조**

**연관관계의 주인만이 FK를 등록, 수정할 수 있으며, 주인이 아닌 쪽은 읽기만 가능하다.**

주인은 mappedBy 속성이 없고, 주인이 아닌 쪽이 mappedBy 속성으로 주인을 지정한다.

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

Member의 team은 @JoinColumn으로 FK TEAM_ID를 관리한다. 즉 team이 연관관계의 주인이다.

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

  @OneToMany(mappedBy = "team") //  Member의 team에 mapping
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

Team의 members는 mappedBy로 연관관계의 주인인 Member의 team과 매핑된다. members는 읽기전용으로 삽입, 수정해봤자 연관관계에 영향을 미치지 않는다. 대신 연관된 팀의 조회는 가능하다.

### 연관관계의 주인 정하기

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%204.png)

**테이블에서 FK가 있는 쪽에 연관관계의 주인이 존재한다. 이게 가장 깔끔하다.**

MEMBER 테이블에 FK TEAM_ID가 존재하므로 Member의 team이 연관관계의 주인이다.

DB에서는 다(Many)인 테이블에 항상 FK가 존재한다.

cf) 연관관계의 주인이 Business적으로 중요함을 의미하지 않는다.

ex) 바퀴 - 자동차 관계는 바퀴가 ‘다’이므로 바퀴에 연관관계의 주인이 존재한다.

### 양방향 연관관계 주의사항

아래 코드를 실행시켜보자.

**JpaMain**

```java
try {
  //  저장
  Member member = new Member();
  member.setUsername("member1");
  em.persist(member);

  Team team = new Team();
  team.setName("teamA");
  team.getMembers().add(member);
  em.persist(team);

  em.flush();
  em.clear();

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

DB 테이블을 확인해보면 결과는 다음과 같다.

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%205.png)

member의 TEAM_ID가 null이다. 객체 team의 참조 members는 읽기 전용이므로 이것을 통해 삽입, 수정을 해봤자 DB에는 영향을 주지 않는다.

올바른 방법은 다음과 같다.

**JpaMain**

```java
try {
  //  저장
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setTeam(team);
  em.persist(member);

  em.flush();
  em.clear();

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

DB 테이블을 확인해보면 결과는 다음과 같다.

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%206.png)

member의 TEAM_ID에 team의 PK인 1이 잘 들어온 것을 확인할 수 있다. 데이터를 삽입, 수정하기 위해서는 연관관계의 주인을 통해서 삽입, 수정해야 한다.

연관관계의 주인이 아닌 참조에 삽입, 수정하는 것 자체는 문제가 되지 않는다. 

**하지만 연관관계의 주인에도 반드시 삽입, 수정을 해줘야 한다.**

**보다 객체지향적인 코드**

**JpaMain**

```java
try {
  //  저장
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setTeam(team);
  em.persist(member);

  em.flush();
  em.clear();

  Team findTeam = em.find(Team.class, team.getId());
  List<Member> members = findTeam.getMembers();

  for(Member m : members) {
    System.out.println("m = " + m.getUsername());
  }

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

위와 같이 연관관계의 주인에만 삽입을 해주어도, 정상적으로 m이 출력이 된다.

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%207.png)

상단의 select 쿼리는 findTeam을 찾는 쿼리이고, 하단의 쿼리는 members를 찾는 쿼리이다.

만약 flush하지 않으면 어떻게 될까?

**JpaMain**

```java
try {
  //  저장
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setTeam(team);
  em.persist(member);

//      em.flush();
//      em.clear();

  Team findTeam = em.find(Team.class, team.getId());  //  1차 캐시에 남아있는 것을 가져옴
  List<Member> members = findTeam.getMembers();

  System.out.println("=======================================");
  for(Member m : members) {
    System.out.println("m = " + m.getUsername());
  }
  System.out.println("=======================================");

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%208.png)

아무것도 출력되지 않는다. clear를 하지 않았으므로 1차 캐시에 남아있는 team을 그대로 가져오는데 team의 members에는 member를 넣어준 적이 없으므로 members에는 아무것도 없기 때문이다.

**이러한 일을 방지하기 위해 연관관계의 주인, 주인이 아닌 쪽 모두에 값을 세팅하는 것이 좋다.** 

**객체지향적으로도 그것이 맞다.**

**JpaMain**

```java
try {
  //  저장
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setTeam(team);
  em.persist(member);

  team.getMembers().add(member);
//      em.flush();
//      em.clear();

  Team findTeam = em.find(Team.class, team.getId());  //  1차 캐시에 남아있는 것을 가져옴
  List<Member> members = findTeam.getMembers();

  System.out.println("=======================================");
  for(Member m : members) {
    System.out.println("m = " + m.getUsername());
  }
  System.out.println("=======================================");

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

![Untitled](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/Untitled%209.png)

1차 캐시에 있음에도 논리적으로 알맞게 출력된다.

**양방향 연관관계 시, 양쪽에 모두 값을 세팅해주자.**

연관관계 편의 메서드를 사용하면 좋다.

**Member의 changeTeam 메서드**

```java
public void changeTeam(Team team) {
  this.team = team;
  team.getMembers().add(this);
}
```

**JpaMain**

```java
try {
  //  저장
  Team team = new Team();
  team.setName("teamA");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.changeTeam(team);
  em.persist(member);

//      em.flush();
//      em.clear();

  Team findTeam = em.find(Team.class, team.getId());  //  1차 캐시에 남아있는 것을 가져옴
  List<Member> members = findTeam.getMembers();

  System.out.println("=======================================");
  for(Member m : members) {
    System.out.println("m = " + m.getUsername());
  }
  System.out.println("=======================================");

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

연관관계 편의 메서드는 한쪽에만 정의해서 사용하는 것이 좋다.

### 무한루프 주의

양방향 연관관계 시 양 객체 모두 상대 객체의 참조를 가지고 있다.

이 때 어느 한 객체를 toString()으로 호출하면 상대 객체의 toString()을 호출하고, 상대 객체도 원 객체의 toString()을 호출하며 무한루프가 발생한다.

또한 DTO가 아니라 Entity를 바로 전달할 경우 JSON으로 변환하는 과정에서 위와 같은 무한 루프가 발생할 수 있다.

Lombok에서 @ToString의 사용을 자제해야 하는 이유이다.

또한 Spring의 Controller에서 Entity를 바로 사용하지 않고 DTO를 통해 전달하는 이유이다. (+ Entity가 오염될 수 있음) 

### 결론

**단방향 매핑으로 먼저 연관관계 매핑 설정을 완료해야 한다. 단방향 매핑만으로 객체-테이블 매핑은 완성된다.**

양방향 매핑은 필요할 때 나중에 추가해도 된다. (JPQL 사용 등)

# Computer Network

## Network Layer

### DHCP

![1.jpeg](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/1.jpeg)

![2.jpg](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/2.jpg)

![3.jpg](24_02_01_daily_certification%2082321062dae1470dbee242840f3e00f7/3.jpg)

# Problem Solving (Algorithm & SQL)

**BOJ 11509 풍선 맞추기**

[11509번: 풍선 맞추기](https://www.acmicpc.net/problem/11509)

높이 별로 맞출 수 있는 화살 개수를 저장하는 배열 arrCnt를 선언한다. 풍선 높이 h를 입력받을 때마다 해당 높이에 화살이 1개 이상 있을 경우, 즉 arrCnt[h] > 0일 경우, arrCnt[h]를 1 감소시키고, arrCnt[h - 1]을 1 증가시킨다. 만약 arrCnt[h]가 0일 경우, 해당 높이를 맞출 수 있는 화살이 없으므로 정답을 1 증가시키고 arrCnt[h - 1]을 1 증가시킨다. 

**코드**

```kotlin
import java.util.*
import java.io.*

const val MAX = 1_000_001

class BOJ_11509 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    
    private var n = 0    //    풍선 개수
    private val arrowCnt = IntArray(MAX)    //    arrowCnt[i] : 높이 i에 있는 화살 개수
    private var ans = 0
    
    fun solve() {
        n = br.readLine().toInt()
        
        tokens = StringTokenizer(br.readLine())
        repeat(n) {
            val ballon = tokens.nextToken().toInt()    //    풍선 높이
            
            if(arrowCnt[ballon] > 0)    //    해당 높이를 맞출 수 있는 화살이 있을 경우
                arrowCnt[ballon]--            //    해당 높이 풍선을 터뜨림
            else    //    해당 높이를 맞출 수 있는 화살이 없을 경우
                ans++    //    화살 하나를 해당 높이로 새로 쏨
            arrowCnt[ballon - 1]++        //    높이 1 감소
        }
        
        println(ans)
    }    
}

fun main() {
    BOJ_11509().solve()
}
```