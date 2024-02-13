# 24_02_14_daily_certification

# JPQL 기본 문법

## Projection

### Entity Projection

**JpaMain**

```kotlin
try {
  Member member = new Member();
  member.setUsername("member1");
  member.setAge(10);
  em.persist(member);

  em.flush(); //  DB에 반영
  em.clear(); //  영속성 컨텍스트 초기화

  List<Member> result = em.createQuery("select m from Member m", Member.class)
      .getResultList(); //  result의 Member들은 영속성 컨텍스트에 의해 관리가 될까?

  Member findMember = result.get(0);
  findMember.setAge(20);  //  update 쿼리가 나갈까? = 영속성 컨텍스트에 의해 관리가 될까?

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

영속성 컨텍스트를 초기화한 후 가져온 result의 원소들은 모두 Member Entity다. 이들이 영속성 컨텍스트에 의해 관리가 될까? 즉 setAge로 나이를 변경했을 때 update 쿼리가 나갈까?

정답은 나간다. 

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled.png)

실제 DB의 AGE도 20으로 갱신되었다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%201.png)

즉 getResultList로 가져온 result의 원소들은 영속성 컨텍스트에 의해 관리된다.(getSingleResult도 마찬가지)

그래서 **Entity Projection**이라 하는 것임.

연관된 Entity도 가져올 수 있다.

**JpaMain**

```kotlin
try {
  Team team = new Team();
  team.setName("team0");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setAge(10);
  member.setTeam(team);

  em.persist(member);

  em.flush(); //  DB에 반영
  em.clear(); //  영속성 컨텍스트 초기화

  List<Team> result = em.createQuery("select m.team from Member m", Team.class)
      .getResultList(); //  result의 Member들은 영속성 컨텍스트에 의해 관리가 될까?

  Team findTeam = result.get(0);
  findTeam.setName("team1");  //  update 쿼리가 나갈까? = 영속성 컨텍스트에 의해 관리가 될까?

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

MEMBER 테이블이 아니라 TEAM 테이블에서 team 정보를 가져오므로 실제 SQL은 JOIN 쿼리가 나간다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%202.png)

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%203.png)

JPQL은 단순한 select 쿼리 같지만 실제 SQL은 복잡한 JOIN 쿼리가 나가므로 괴리가 심하다. (묵시적 JOIN)

실제로 JOIN 같은 고비용 쿼리가 나가는 것을 JPQL에서도 알 수 있어야 한다. 

**즉, JPQL을 실제 SQL과 유사하게 작성해야 한다.**

다음과 같이 JOIN임을 알 수 있게 작성하는 것이 좋다. (명시적 JOIN)

```java
List<Team> result = em.createQuery("select t from Member m join m.team t", Team.class)
          .getResultList();
```

어차피 같은 JOIN 쿼리가 나간다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%204.png)

### Embedded Projection

**JpaMain**

```java
try {
  Team team = new Team();
  team.setName("team0");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setAge(10);
  member.setTeam(team);

  em.persist(member);

  em.flush(); //  DB에 반영
  em.clear(); //  영속성 컨텍스트 초기화

  em.createQuery("select o.address from Order o", Address.class).getResultList();

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

Address 테이블이 없으므로 Address에서 바로 가져오진 못하고, Address가 속한 Entity로부터 꺼내와야 한다.

### Scalar Projection

**JpaMain**

```java
try {
  Team team = new Team();
  team.setName("team0");
  em.persist(team);

  Member member = new Member();
  member.setUsername("member1");
  member.setAge(10);
  member.setTeam(team);

  em.persist(member);

  em.flush(); //  DB에 반영
  em.clear(); //  영속성 컨텍스트 초기화

  em.createQuery("select distinct m.username, m.age from Member m").getResultList();

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

Member의 username(String), age(int)와 같이 Entity도 아니면서 한 개 이상의 타입의 값을 동시에 가져오는 것을 Scalar Projection이라고 한다. 일반 SQL의 select 쿼리 사용과 가장 유사한 쿼리이다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%205.png)

이럴 경우 특정 타입으로 매핑되는 것이 아니라서 어떻게 사용해야 할지 난감하다.

**Query 사용 JpaMain**

반환 타입이 명확하지 않을 때 사용하는 Query를 사용해 Object List를 받고, 각 원소를 Object[]로 형변환해서 사용한다.

```java
List resultList = em.createQuery("select distinct m.username, m.age from Member m").getResultList();
Object o = resultList.get(0);
Object[] result = (Object[])o;
System.out.println("username(result[0]) = " + result[0]);
System.out.println("age(result[1]) = " + result[1]);
```

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%206.png)

**TypeQuery 사용 JpaMain**

Object[]의 TypeQuery를 사용해 Object[] List를 받고, 각 원소를 사용한다.

```java
List<Object[]> resultList = em.createQuery("select distinct m.username, m.age from Member m", Object[].class).getResultList();
Object[] result = resultList.get(0);
System.out.println("username(result[0]) = " + result[0]);
System.out.println("age(result[1]) = " + result[1]);
```

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%206.png)

Query, TypeQuery 둘 다 나쁘진 않지만 뭔가 깔끔하지 못한게 사실이다.

DTO를 이용하여 깔끔하게 해결할 수 있다.

먼저 Member에서 가져올 스칼라들을 모아둔 DTO MemberDTO 클래스를 정의한다.

**MemberDTO**

```java
public class MemberDTO {
  private String username;
  private int age;

  /* getter, setter, all args constructor */
}
```

**DTO 사용 JpaMain**

Object[]의 TypeQuery를 사용해 Object[] List를 받고, 각 원소를 사용한다.

```java
List<MemberDTO> resultList = em.createQuery("select new jpql.MemberDTO(m.username, m.age) from Member m", MemberDTO.class).getResultList();
MemberDTO result = resultList.get(0);
System.out.println("username = " + result.getUsername());
System.out.println("age = " + result.getAge());
```

해당 값들을 가져와 대입한 후 DTO 객체들을 new를 통해 생성하고 이를 가져와 사용한다고 생각하면 된다.

이를 위해선 모든 field를 초기화하는 생성자가 필요하다.

단점은 DTO 클래스가 위치한 패키지 깊이가 깊을 수록 패키지 명이 길어진다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%207.png)

# Problem Solving (Algorithm & SQL)

**BOJ 2109 순회강연**

[2109번: 순회강연](https://www.acmicpc.net/problem/2109)

이전에 풀었던 1781 컵라면 문제와 유사하다.

[1781번: 컵라면](https://www.acmicpc.net/problem/1781)

우선순위 큐에는 강연할 수 있는 강의들만 담겨 있다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%208.png)

우선순위 큐의 크기가 곧 강연할 날의 수이다. 강연의 d보다 우선순위 큐의 크기가 크다면 해당 강연을 하기에는 이미 잡혀있는 강연이 너무 많은 것이다. 그럴 경우 해당 강연을 포함하여 기존에 잡혀있는 강연 중 가장 가치가 작은 강연부터 배제한다.

일단 강연 날짜가 촉박한 것부터 해결해 나가야 하므로 강연 날짜가 빠른 순으로 정렬한다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%209.png)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2109 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var r = 0
    private var c = 0
    private lateinit var table : Array<CharArray>
    private val set = HashSet<String>()
    private var count = 0

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        r = tokens.nextToken().toInt()
        c = tokens.nextToken().toInt()

        table = Array(r) { CharArray(c) }
        for(i in 0 ..< r)
            table[i] = br.readLine().toCharArray()

        parametricSearch()
        println(count)
    }

    private fun parametricSearch() {
        var start = 0
        var end = r - 1

        while(start <= end) {
            val mid = (start + end) / 2 //  mid행부터 부분 문자열을 확인해봄

            set.clear()
            for(j in 0 ..< c) {
                sb.clear()
                for(i in mid ..< r)
                    sb.append(table[i][j])
                set.add(sb.toString())
            }

            if(set.size == c) { //  mid행부터 문자열 잘랐을 때 중복 없을 경우
                count = mid     //  일단 count 저장
                start = mid + 1 //  더 큰 count 찾아보기
            }
            else  //  mid행부터 문자열 잘랐을 때 중복 발생할 경우
                end = mid - 1   //  더 작은 count 찾아야 함
        }
    }
}

fun main() {
    BOJ_2109().solve()
}
```

# 독서

**내가 틀릴 수도 있습니다. -비욘나티코린데블라드-**

**Q. 오늘 읽은 부분 중 기억에 남는 문장?**

**A.**