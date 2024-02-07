# 24_02_07_daily_certification

# JPA

## Proxy

### 문제 제기

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled.png)

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

## Proxy

### em.find() vs em.getReference()

- em.find() : DB 조회를 통해 실제 Entity 객체를 조회한다.
- em.getReference() : DB 조회를 미루고 Proxy Entity객체를 조회한다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%201.png)

다음 예시를 보자.

```java
Member findMember = em.find(Member.class, member.getId());
```

위 코드를 실행하면 다음과 같은 select 쿼리가 나온다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%202.png)

하지만 다음 코드를 실행하면 select 쿼리가 나가지 않는다.

```java
Member findMember = em.getReference(Member.class, member.getId());
```

만약 em.getReference()로 member를 찾고 이를 다음과 같이 출력한다면

```java
Member findMember = em.getReference(Member.class, member.getId());
System.out.println("findMember.id = " + findMember.getId());
System.out.println("findMember.username = " + findMember.getUsername());
```

em.find()로 조회했을 때와 같은 select 쿼리가 나가게 된다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%203.png)

자세히 보면 id를 조회할 때는 em.getReference()로 찾을 때 id 값을 넣었기 때문에 select 쿼리가 나가지 않지만, username을 조회할 땐 select 쿼리가 나간다.

id는 proxy entity에서 바로 가져올 수 있어서 select 쿼리가 나갈 필요가 없지만, username은 DB 테이블 내에 존재하므로 select 쿼리가 나간다.

findMember의 class를 getClass()로 찾아서 출력해보면

```java
Member findMember = em.getReference(Member.class, member.getId());
System.out.println("findMember = " + findMember.getClass());
```

아래와 같이 Member가 아니라 괴랄한 클래스가 출력되는 것을 알 수 있다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%204.png)

Hibernate가 만들어낸 Proxy class가 출력된다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%201.png)

Proxy 객체의 target이 진짜 Entity 객체를 가리킨다.

**Proxy 객체의 특징**

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%205.png)

Proxy 클래스는 실제 Entity 클래스를 상속받아서 만들어진다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%206.png)

Proxy 객체의 target은 Proxy 객체의 원래 객체를 가리킨다. 최초에는 null로 설정되어 있다.

Proxy 객체의 메서드를 호출하면 실제 객체의 메서드를 호출한다.

target이 최초에 어떻게 설정될까?

**Proxy 객체 초기화 과정**

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%207.png)

**영속성 컨텍스트를 통해서 초기화를 요청한다.**

초기화 전, 후 두번 getUsername()을 호출하면

```java
//  DB Proxy 조회
Member findMember = em.getReference(Member.class, member.getId());
System.out.println("findMember.id = " + findMember.getId());
System.out.println("findMember.username = " + findMember.getUsername());
System.out.println("findMember.username = " + findMember.getUsername());
```

Proxy 객체 초기화를 위한 DB 조회 select 쿼리는 1번만 나간 후, 다음에는 DB 조회 없이 초기화된 Proxy 객체 값을 바로 가져오는 것을 알 수 있다. 

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%208.png)

## Proxy의 특징

- 프록시 객체는 처음 사용할 때 한 번만 초기화
- **프록시 객체를 초기화 할 때, 프록시 객체가 실제 엔티티로 바뀌는 것은 아님, 초기화되면 프록시 객체를 통해서 실제 엔티티에 접근 가능**
- **프록시 객체는 원본 엔티티를 상속받음, 따라서 타입 체크시 주의해야함 (== 비교 실패, 대신 instance of 사용)**
- 영속성 컨텍스트에 찾는 엔티티가 이미 있으면 em.**getReference()**를 호출해도 실제 엔티티 반환
- 영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일 때, 프록시를 초기화하면문제 발생 (하이버네이트는 org.hibernate.LazyInitializationException 예외를 터트림)

**프록시 객체는 원본 엔티티를 상속받음, 따라서 타입 체크시 주의해야함 (== 비교 실패, 대신 instance of 사용)**

아래와 같은 로직을 수행할 때

```java
private static void logic(Member m1, Member m2) {
  System.out.println("m1 == m2 : " + (m1.getClass() == m2.getClass()));
}
```

member1, member2를 둘 다 find로 찾고 클래스를 비교하면

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  Member member2 = new Member();
  member2.setUsername("member2");
  em.persist(member2);

  em.flush();
  em.clear();

  Member m1 = em.find(Member.class, member1.getId());
  Member m2 = em.find(Member.class, member2.getId());

  logic(m1, m2);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

당연히 같은 Member 클래스로 true가 출력된다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%209.png)

member2는 getReference로 가져온 후 클래스를 비교하면

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  Member member2 = new Member();
  member2.setUsername("member2");
  em.persist(member2);

  em.flush();
  em.clear();

  Member m1 = em.find(Member.class, member1.getId());
  Member m2 = em.getReference(Member.class, member2.getId());

  logic(m1, m2);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

member1은 Member 클래스, member2는 Member를 상속받는 Proxy 클래스이므로 false가 출력된다. 

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2010.png)

실제 비즈니스 로직에서 Entity 타입을 받을 땐 Entity 객체를 받을지, Proxy 객체를 받을 지 알 수 없기 때문에 절대 타입 비교를 ==으로 해서는 안된다.

올바른 코드와 실행 결과는 다음과 같다.

```java
private static void logic(Member m1, Member m2) {
  System.out.println("m1 == Member : " + (m1 instanceof Member));
  System.out.println("m2 == Member : " + (m2 instanceof Member));
}
```

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2011.png)

**영속성 컨텍스트에 찾는 엔티티가 이미 있으면 em.getReference()를 호출해도 실제 엔티티 반환**

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  em.flush();
  em.clear();

  Member m1 = em.find(Member.class, member1.getId());
  System.out.println("m1 = " + m1.getClass());

  Member reference = em.getReference(Member.class, member1.getId());
  System.out.println("reference = " + reference.getClass());
	System.out.println("m1 == reference : " + (m1 == reference));

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

m1을 find로 실제 DB에서 조회한 후, m1을 getReference로 Proxy로 조회를 시도해밨다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2012.png)

reference도 Proxy 클래스가 아니라 Member 클래스가 출력된다. Proxy가 아니라 원본이 가져와진다.

**JPA는 같은 객체는 같은 Persistence Context 내에서, 한 Transaction 내에서 == 비교는 항상 true가 나와야 한다.**

그리고 이미 원본을 가져왔는데 굳이 Proxy를 가져와서 얻을 이익이 없다.

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  em.flush();
  em.clear();

  Member m1 = em.getReference(Member.class, member1.getId());
  System.out.println("m1 = " + m1.getClass());

  Member reference = em.getReference(Member.class, member1.getId());
  System.out.println("reference = " + reference.getClass());
	System.out.println("m1 == reference : " + (m1 == reference));

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

둘 다 Proxy로 가져올 경우에도 당연히 같은 Proxy가 가져와지며 == 비교 시 true이다. Proxy를 가져왔으므로 select 쿼리는 나오지 않느다.

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2013.png)

반대로 Proxy를 가져온 객체를 find하려 하면 어떻게 될까?

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  em.flush();
  em.clear();

  Member refMember = em.getReference(Member.class, member1.getId());
  System.out.println("refMember = " + refMember.getClass());

  Member findMember = em.find(Member.class, member1.getId());
  System.out.println("findMember = " + findMember.getClass());
  System.out.println("refMember == findMember : " + (refMember == findMember));

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2014.png)

refMember가 Proxy가 되는건 당연하다. findMember 또한 Proxy를 가져온다.

만약 findMember를 원본을 가져오면 refMember == findMember가 true임을 보장할 수 없다.

**JPA는 같은 객체는 같은 Persistence Context 내에서, 한 Transaction 내에서 == 비교는 항상 true가 나와야 한다.**

**영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일 때, 프록시를 초기화하면문제 발생**

Proxy를 가져오고 username에 접근하면 DB를 조회하며 Proxy가 잘 초기화된다.

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  em.flush();
  em.clear();

  Member refMember = em.getReference(Member.class, member1.getId());
  System.out.println("refMember = " + refMember.getClass());  //  Proxy

  System.out.println("refMember.name = " + refMember.getUsername());

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2015.png)

Proxy를 가져오고 Persistence Context를 종료한 상태에서 Proxy의 초기화를 시도하면 어찌될까?

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  em.persist(member1);

  em.flush();
  em.clear();

  Member refMember = em.getReference(Member.class, member1.getId());
  System.out.println("refMember = " + refMember.getClass());  //  Proxy

	em.detach(refMember); //  준영속 상태로 변경
//      em.close();           //  영속성 컨텍스트 종료
//      em.clear();           //  영속성 컨텍스트 초기화

  System.out.println("refMember.name = " + refMember.getUsername());

  tx.commit();
} catch (Exception e) {
  tx.rollback();
	e.printStackTrace();
} finally {
  em.close();
}
```

준영속 상태로 변경한 경우, 또는 영속성 컨텍스트를 초기화한 경우

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2016.png)

Proxy를 초기화할 수 없다는 LazyInitializationException이 발생한다. no Session이 Persistence Context에서 관리되지 않는다는 뜻이다.

영속성 컨텍스트를 종료한 경우

![Untitled](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/Untitled%2017.png)

영속성 컨텍스트가 닫혔다는 뜻이다.

# Problem Solving (Algorithm & SQL)

**BOJ 12014 주식**

[12014번: 주식](https://www.acmicpc.net/problem/12014)

LIS의 길이를 구하는 문제이다. 테스트케이스 개수 T가 최대 100이고, N이 최대 10^4이므로 DP를 이용한 O(N^2) 풀이는 아마 시간 초과가 날 것이다. 따라서 이분 탐색을 이용한 O(NlogN) 풀이를 고안해야 한다.

![lis.jpeg](24_02_07_daily_certification%20435a7bdf92d7415b9079b7b7556f4648/lis.jpeg)

초기에 LIS 리스트에 더미 값 Int.MIN_VALUE를 넣고 시작한다. LIS에는 수가 오름차순으로 저장된다.

주어진 수열의 수를 하나씩 확인한다.

1.  LIS에 저장된 마지막 값보다 클 경우 
    
    **LIS에 추가한다. 이 경우 LIS의 길이가 1증가한다.**
    
2. LIS에 저장된 마지막 값보다 작을 경우
    
    **LIS에 저장된 수 중 해당 수보다 크거나 같은 최소 수를 찾는다. 이 때 이분탐색을 이용해 O(logN)에 찾을 수 있다. 찾은 수를 현재 수열의 수로 대체한다.**
    

수열의 모든 수를 확인한 후, 더미 값을 제외한 LIS의 길이 - 1이 정답이다.

**코드**

```kotlin
import java.io.*
import java.util.*

class BOJ_12014 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer
    private val sb = StringBuilder()
    
    private val lis = ArrayList<Int>()
    
    private var t = 0
    private var n = 0
    private var k = 0
    
    fun solve() {
        t = br.readLine().toInt()
        
        repeat(t) {
            sb.append("Case #${it + 1}\n")
            
            tokens = StringTokenizer(br.readLine())
            n = tokens.nextToken().toInt()
            k = tokens.nextToken().toInt()
        
            lis.clear()
            lis.add(Int.MIN_VALUE)
            
            tokens = StringTokenizer(br.readLine())
            for(i in 0 ..< n) {
                val num = tokens.nextToken().toInt()
                
                if(lis[lis.size - 1] < num)    //   기존에 가장 큰 값보다 더 큰 값이 들어올 경우 LIS 길이 1 증가
                    lis.add(num)
                else {    //    num보다 크거나 같은 수 중 가장 작은 수 대체 
                    val idx = binarySearch(num)
                    lis[idx] = num
                }
            }
        
            sb.append("${if(lis.size - 1 >= k) 1 else 0}\n")
        }
        
        print(sb)
    }
    
    private fun binarySearch(num : Int) : Int {
        var start = 0
        var end = lis.size - 1
        
        while(start < end) {
            val mid = (start + end) / 2
            
            if(lis[mid] >= num)     //    크거나 같은 수 찾았을 경우
                end = mid           //    일단 저장
            else                    //    작을 경우
                start = mid + 1     //    더 오른쪽에서 찾아야 함
        }
        
        return end
    }
}

fun main() {
    BOJ_12014().solve()
}
```