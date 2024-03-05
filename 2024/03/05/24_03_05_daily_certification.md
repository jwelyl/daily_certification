# 24_03_05_daily_certification

# 주요 대기업 지원 준비

[주요 기업 서류 지원 준비](https://www.notion.so/ca46129d4559498794f2f81c628d75a5?pvs=21)

# JPQL

## Fetch Join

JPQL에서 성능 최적화를 위해 제공하는 기능으로, **연관된 Entity나 Collection을 SQL한 번에 함께 조회한다.**

ex) 회원을 조회하면서 연관된 팀도 함께 조회하고 싶을 경우(SQL 한 번에)

**JPQL**

```sql
select m from Member m join fetch m.team;
```

**SQL**

```sql
SELECT M.*, T.* FROM MEMBER M INNER JOIN TEAM T on M.TEAM_ID = T.ID;
```

MEMBER와 TEAM의 관계가 다음과 같을 때

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled.png)

MEMBER와 TEAM의 inner join 테이블은 다음과 같다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%201.png)

inner join이므로 연관된 TEAM 정보가 없는 회원4의 정보는 누락된다.

위의 상황에서 다음과 같은 jpql을 실행하면

**JPQL**

```sql
select m from Member m join fetch m.team;
```

1차 캐시에는 다음과 같이 보관된다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%202.png)

위와 같은 상황을 실제 코드로 재현해보자.

**JpaMain**

```java
Team teamA = new Team();
teamA.setName("teamA");
em.persist(teamA);

Team teamB = new Team();
teamB.setName("teamB");
em.persist(teamB);

Team teamC = new Team();
teamC.setName("teamC");
em.persist(teamC);

Member member1 = new Member();
member1.setUsername("회원1");
member1.setAge(10);
member1.setTeam(teamA);
member1.setType(MemberType.ADMIN);

em.persist(member1);

Member member2 = new Member();
member2.setUsername("회원2");
member2.setAge(20);
member2.setTeam(teamA);
member2.setType(MemberType.USER);

em.persist(member2);

Member member3 = new Member();
member3.setUsername("회원3");
member3.setAge(30);
member3.setTeam(teamB);
member3.setType(MemberType.USER);

em.persist(member3);

Member member4 = new Member();
member4.setUsername("회원4");
member4.setAge(25);
member4.setType(MemberType.ADMIN);

em.persist(member4);

em.flush(); //  DB에 반영
em.clear(); //  영속성 컨텍스트 초기화
```

먼저 Member 정보만 가져와보자.

```java
String query = "select m from Member m";

List<Member> result = em.createQuery(query, Member.class).getResultList();

for(Member member : result) {
  System.out.println("member = " + member);
}
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%203.png)

member 정보만 출력하려 하니까 1번의 select Member 쿼리가 나간다.

```java
List<Member> result = em.createQuery(query, Member.class).getResultList();

for(Member member : result) {
  System.out.println("member = " + member.getUsername() + " " + member.getTeam().getName());
}
```

만약 member에서 추가로 team 정보를 가져오려 한다면

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%204.png)

지연 로딩(LAZY)이므로 team은 Proxy로 들어온다 . 따라서 team에 관련된 정보를 가져오려 할 때만 select Team 쿼리를 실행해서 team 정보를 가져온다.

처음 select Member로 member 4개를 Proxy team과 함께 가져오고, 4개 member를 루프를 돌면서 team의 정보를 최초로 가져올 때마다 team을 SQL로 가져온다.

**회원1의 team 정보인 teamA를 가져올 때는 team이 Proxy라서 select Team 쿼리가 나간다. 회원2의 team 정보인 teamA는 이제 Proxy가 아니라 영속성 1차 캐시에 존재하므로 쿼리가 나가지 않고, 회원3의 team 정보인 teamB는 team이 Proxy이므로 select Team 쿼리가 나간다.**

### **N + 1 문제**

벌써 심각함이 느껴지지 않는가? 만약 회원이 1,000,000명이고 회원 모두 다른 팀 소속이면,

select Member 쿼리 1번에 select Team 쿼리 100만번이 나갈 것이다.

### Fetch Join

N + 1 문제를 해결할 수 있는 사실상 유일한 방법이다.

```java
String query = "select m from Member m join fetch m.team";

List<Member> result = em.createQuery(query, Member.class).getResultList();

for (Member member : result) {
  System.out.println("member = " + member.getUsername() + " " + member.getTeam().getName());
}
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%205.png)

최초에 Member와 Team 정보를 join으로 다 가져온다. 따라서 루프를 돌 때 team은 Proxy가 아니라 정말 Team Entity이다.

**지연 로딩으로 설정해도 Fetch Join이 우선이다.**

Fetch Join은 기본은 inner join이지만 left join도 가능하다.

위의 예제는 Member에서 Team을 가져오는 다대일(@ManyToOne) 관계에서 Fetch Join인데, 일대다(@OneToMany)에서도 가능하다.

```java
String query = "select t from Team t join fetch t.members";

List<Team> result = em.createQuery(query, Team.class).getResultList();

for (Team team : result) {
  System.out.println("team = " + team.getName() + " | members = " + team.getMembers().size());
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%206.png)

teamA의 정보가 2번 출력된 것을 알 수 있다. 왜 그럴까?

 **일대다(@OneToMany) join에서는 결과로 가져오는 데이터가 중복될 수 있다.**

Team과 Member의 관계는 아래와 같다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%207.png)

join해서 team.id = 1인 결과만 가져오면 아래와 같다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%208.png)

JPA 입장에서는 어쩔 수 없다. teamA에 속한 member가 1명인지 2명인지, 100만명인지 알 방법이 없이 전부 가져올 수 밖에 없다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%209.png)

teamA는 같은 영속성 컨텍스트안에 존재하는 같은 객체지만 join 결과 테이블에 2개 존재하므로 같은 주소값이 2개 있게 된다.

```java
for (Team team : result) {
  System.out.println("team = " + team.getName() + " | members = " + team.getMembers().size());
  for(Member member : team.getMembers()) {
    System.out.println("-> member = " + member);
  }
}
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2010.png)

중복된 teamA가 2번 출력되는 게 별로면 JPQL의 distinct를 사용하면 된다.

SQL의 distinct는 row 전체가 완전히 같아야 중복이라고 간주하므로 SQL의 distinct로는 중복을 제거할 수 없다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%208.png)

row 1과 row 2는 teamA만 같고 member 정보가 다르므로 SQL의 distinct를 사용해도 중복으로 간주되지 않는다.

JPQL으 distinct는 추가적으로 어플리케이션에서 같은 식별자를 가진 Team Entity를 제거한다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2011.png)

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2012.png)

### Fetch Join vs Join

위의 일대다 Fetch Join 시나리오를 일반 join으로 재현해보자.

```java
String query = "select t from Team t join t.members";
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2013.png)

join 결과로 3개를 가져오는 것은 같지만 결과적으로 team의 결과만 가져오고 Member 컬렉션은 가져와지지 않는다. 따라서 team의 member 정보를 조회할 때마다 select Member 쿼리가 추가로 나간다.

**일반 join은 연관된 Entity를 가져오지 않는다.**

**Fetch Join을 사용할 때만 연관된 Entity도 함께 조회한다.(즉시 로딩)**

**Fetch Join은 객체 그래프를 SQL 한 번에 조회하는 개념이다.**

### Fetch Join의 한계

- Fetch Join의 대상에는 alias를 줄 수 없다. Hiberate에서는 가능하다지만 안 쓰는 게 좋다.
    
    Fetch Join은 애초에 연관된 정보를 모두 가져오는게 목적이다. alias를 통해 일부만 가져오고 싶다면 Fetch Join을 사용할 게 아니라 별도의 쿼리를 사용하는 등 다른 방법을 사용해야 한다.
    
- 둘 이상의 Collection은 Fetch Join할 수 없다.
    
    Team과 일대다 관계인 Entity가 Member 외에도 Orders도 있다고 가정하면, Member와 Orders와 동시에 Fetch Join하면 안된다. 문법 상 안되는 것보다도 데이터의 정합성이 맞지 않는다.
    
- Collection을 Fetch Join하면  데이터 중복이 발생하므로 Paging API를 사용할 수 없다.
    
    역시 데이터 정합성 문제다. 사용하면 경고가 발생하며 모든 정보를 메모리에 가져온다. 
    
    ![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2014.png)
    
    ![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2015.png)
    
    쿼리를 보면 페이징 쿼리가 없이 전부 다 가져온다. 만약 100만건이 있으면 100만건을 모두 메모리에 가져온다.
    
    ![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%208.png)
    
    위의 예시에서 Paging으로 1개만 가져온다고 가정하면 첫 번째 row만 가져오면 teamA에는 member1만 존재한다는 결과가 나오게 된다.
    

### Fetch Join과 Paging

- 정 페이징을 하고 싶으면 일대다 Fetch Join을 하지 말고 다대일 Fetch Join을 하거나
- **Batch Size를 조절하자.**
- 아니면 DTO를 새로 정의하자.

### Batch Size

**Team Entity**

```java
@Entity
public class Team {
  @Id
  @GeneratedValue
  private Long id;
  private String name;

  @BatchSize(size = 100)
  @OneToMany(mappedBy = "team")
  private List<Member> members = new ArrayList<>();

  public Long getId() {
    return id;
  }

  public void setId(Long id) {
    this.id = id;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public List<Member> getMembers() {
    return this.members;
  }
}
```

@OneToMany 컬렉션 members 위에 **@BatchSize를 설정할 수 있다.**

**JpaMain**

```java
String query = "select t from Team t";

List<Team> result = em.createQuery(query, Team.class).setFirstResult(0).setMaxResults(2).getResultList();
System.out.println("result = " + result.size());

for (Team team : result) {
  System.out.println("team = " + team.getName() + " | members = " + team.getMembers().size());
  for(Member member : team.getMembers()) {
    System.out.println("-> member = " + member);
  }
}
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2016.png)

Fetch Join을 안했으므로 Team을 가져올 때 Member는 Lazy Loading으로 가져오므로 Proxy로 가져와야 맞다. 하지만 Batch Size를 설정하면 1번의 Team을 가져오는 쿼리와 N번의 Member를 가져오는 쿼리 대신에, 한 번에 Batch Size만큼의 Member를 가져와 총 1번의 Team 쿼리와 (N / Batch Size)번의 Member를 가져오는 쿼리가 나가게 된다.

Batch Size는 1000 이하의 적당한 값으로 정하는 것이 좋다.

**persistence.xml**

```xml
<property name="hibernate.default_batch_fetch_size" value="100"/>
```

persistence.xml에서 global하게 정할 수도 있다.

**JPA의 대부분의 성능 문제는 N + 1 문제에서 기인한다.**

**N + 1 문제는 대부분 Fetch Join으로 해결된다.**

## 다형성 쿼리

교재 참고

## Entity 직접 사용

JPQL에서 Entitiy를 직접 사용하면 SQL에서는 해당 Entity의 PK를 사용한다.

**JPQL**

```sql
select count(m.id) from Member m;  //  Entity의 id를 사용
select count(m) from Membert m;    //  Entity를 직접 사용
```

위 두 jpql은 모두

**SQL**

```sql
select count(m.id) as cnt from Member m;
```

위의 sql로 변환된다.

**JpaMain**

```java
String query = "select m from Member m where m = :member";

Member findMember = em.createQuery(query, Member.class).setParameter("member", member1).getSingleResult();
System.out.println("findMember = " + findMember);
```

```java
String query = "select m from Member m where m.id = :memberId";

Member findMember = em.createQuery(query, Member.class).setParameter("memberId", member1.getId()).getSingleResult();
System.out.println("findMember = " + findMember);
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2017.png)

**FK도 마찬가지다.**

**JpaMain**

```java
String query = "select m from Member m where m.team = :team";

List<Member> result = em.createQuery(query, Member.class).setParameter("team", teamA).getResultList();
for(Member member : result) {
  System.out.println("member = " + member);
}
```

```java
String query = "select m from Member m where m.teamId = :teamId";

List<Member> result = em.createQuery(query, Member.class).setParameter("teamId", teamA.getId()).getResultList();
for(Member member : result) {
  System.out.println("member = " + member);
}
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2018.png)

## Named Query

미리 정의해서 이름을 부여해서 사용하는 JPQL이다. 정적 쿼리만 가능하다.

Application 로딩 시점에 초기화 후 재사용하며, **로딩 시점에 쿼리를 검증한다.**

Parsing해서 SQL로 캐시해둔 후 사용한다.

**Application 로딩 시점에 쿼리를 검증한다는 것이 가장 큰 메리트이다.**

**Member Entity**

```java
@Entity
@NamedQuery(
    name = "Member.findByUsername",
    query = "select m from Member m where m.username = :username"
)
public class Member {
  @Id
  @GeneratedValue
  private Long id;
  private String username;
  private int age;

  @ManyToOne(fetch = FetchType.LAZY)
  @JoinColumn(name = "TEAM_ID")
  private Team team;

  @Enumerated(EnumType.STRING)
  private MemberType type;

  public void setType(MemberType type) {
    this.type = type;
  }

  public MemberType getType() {
    return type;
  }

  public Long getId() {
    return id;
  }

  public void setId(Long id) {
    this.id = id;
  }

  public String getUsername() {
    return username;
  }

  public void setUsername(String username) {
    this.username = username;
  }

  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }

  public Team getTeam() {
    return team;
  }

  public void setTeam(Team team) {
    this.team = team;
  }

  public void changeTeam(Team team) {
    this.team = team;
    team.getMembers().add(this);
  }

  @Override
  public String toString() {
    return "Member{" +
        "id=" + id +
        ", username='" + username + '\'' +
        ", age=" + age +
        '}';
  }
}

```

Entity 정의 위에 **@NamedQuery** 어노테이션으로 정의한다.

**JpaMain**

```java
List<Member> result = em.createNamedQuery("Member.findByUsername", Member.class).setParameter("username", "회원1").getResultList();

for (Member member : result) {
  System.out.println("member = " + member);
}

```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2019.png)

persistence.xml에 ormMember.xml을 추가해서 NamedQuery를 추가할 수도 있으며 이 경우에는 xml이 우선권을 가진다. 자세한 내용은 강의 자료를 참고하자.

**Spring Data JPA에서 Repository의 interface method 위에 @Query 어노테이션으로 JPQL을 등록한 경험이 있을텐데 이것을 @NamedQuery로 등록한다. 따라서 문법 오류를 바로 잡아준다.**

## Bulk 연산

여러 개의 데이터를 update, delete하는 연산이다.

**executeUpdate()**

ex) 재고가 10개 미만인 모든 상품의 가격을 10% 인상하기.

Bulk 연산이 없다면?

1. 재고가 10개 미만인 상품을 리스트로 조회한다.
2. 상품 Entity의 가격을 10% 증가시킨다.
3. Transaction Commit 시점에 Dirty Checking으로 변경 감지가 동작한다.

**만약 변경된 데이터가 100만 건이라면 100만번의 UPDATE SQL이 발생한다.**

**JpaMain**

```java
//  모든 회원의 나이를 20살로
//  FLUSH 자동 호출
int resultCount = em.createQuery("update Member m set m.age = 20").executeUpdate();
System.out.println("resultCount = " + resultCount);

```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2020.png)

영향을 받은 회원은 4명이지만 update 쿼리는 단 한 번 나갔다.

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2021.png)

**주의 사항**

Bulk 연산은 영속성 컨텍스트를 무시하고 DB에 직접 쿼리를 보낸다.

```java
//  모든 회원의 나이를 20살로
int resultCount = em.createQuery("update Member m set m.age = 20").executeUpdate();
System.out.println("resultCount = " + resultCount);

System.out.println("member1 나이 = " + member1.getAge());
```

![Untitled](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/Untitled%2022.png)

DB에는 모든 member의 나이가 20으로 반영이 되었지만 영속성 컨텍스트에는 반영이 되지 않았다.

해결 방법

1. 벌크 연산을 먼저 실행하거나
2. **벌크 연산을 수행한 후 영속성 컨텍스트 초기화**

```java
//  모든 회원의 나이를 20살로
int resultCount = em.createQuery("update Member m set m.age = 20").executeUpdate();
System.out.println("resultCount = " + resultCount);

em.clear(); //  Bulk 연산 후 영속성 컨텍스트 초기화
Member findMember = em.find(Member.class, member1.getId());

System.out.println("findMember 나이 = " + findMember.getAge());
```

Spring Data JPA에서는 

# Problem Solving (Algorithm & SQL)

## 코딩 테스트 대비 프로그래머스 문제

[프로그래머스 복습할 문제](https://www.notion.so/1163cecfe9474113bd7ced09c4af4d19?pvs=21)

**BOJ 19641 중첩 집합 모델**

[19641번: 중첩 집합 모델](https://www.acmicpc.net/problem/19641)

![ex.jpeg](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/ex.jpeg)

방문 순서 order를 최초 1로 정해놓고, 루트 노드부터 DFS로 탐색하면 된다. DFS로 노드 v를 탐색할 때는 v를 방문처리한 다음에 v의 left 값을 order로 놓고 order를 1 증가시킨다. v의 인접 노드 중 아직 탐색하지 않은 노드들이 자식 노드들이 되므로 자식 노드들을 DFS로 재귀적으로 탐색한다. 모든 자식 노드들을 탐색한 후에 v의 right 값을 order로 놓고 order를 1 증가시키면 된다.

![ex1.jpeg](24_03_05_daily_certification%20d6201a2d0bb4407496edcf176fcb88fc/ex1.jpeg)

루트가 정해지면 root로부터 DFS를 하면 된다. 인접 리스트로 트리를 구현했을텐데(정점 개수가 최대 10^5개이므로 그래야만 한다.) 각 정점 별 인접 리스트를 오름차순으로 정렬하여 자식 정점 중 번호가 작은 정점부터 탐색하도록 한다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_19641 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens : StringTokenizer
    
    private var n = 0        //    노드 개수
    private var root = 0     //    루트 노드
    
    private lateinit var left : IntArray     //    left[i] : i번째 노드의 left 값
    private lateinit var right : IntArray    //    right[i] : i번째 노드의 right 값
    
    private lateinit var tree : Array<ArrayList<Int>>    //    중첩 집합 모델 트리
    private lateinit var visited : BooleanArray
    
    private var order = 1    //    방문 순서
    
    fun solve() {
        n = br.readLine().toInt()
        
        left = IntArray(n + 1)
        right = IntArray(n + 1)
        tree = Array(n + 1) { ArrayList() }
        visited = BooleanArray(n + 1)
        
        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            
            val node = tokens.nextToken().toInt()    //    간선 정보 받을 노드
            
            while(tokens.hasMoreTokens()) {
                val adjNode = tokens.nextToken().toInt()    //    해당 노드와 연결된 노드
                
                if(adjNode == -1)
                    break
                
                tree[node].add(adjNode)
                tree[adjNode].add(node)    //    양방향
            }
            
            tree[node].sort()    //    노드의 인접 노드들을 오름차순으로 정렬
        }
        
        root = br.readLine().toInt()
        dfs(root)    //    루트부터 탐색
        
        repeat(n) {
            sb.append("${it + 1} ${left[it + 1]} ${right[it + 1]}\n")
        }
        
        print(sb)
    }
    
    private fun dfs(v : Int) {
        visited[v] = true
        left[v] = order++    //    방문한 정점 v의 left에 현재 순서 기록
    
        for(nv in tree[v]) {    //    v의 인접 정점 nv
            if(!visited[nv])
                dfs(nv)    //    아직 방문하지 않은 정점 nv를
        }
        
        right[v] = order++    //    인접 정점 모두 방문한 다음 순서를 v의 right에 기록
    }   // dfs-end
}

fun main() {
    BOJ_19641().solve()
}
```