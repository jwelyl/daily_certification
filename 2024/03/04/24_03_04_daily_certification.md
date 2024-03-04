# 24_03_04_daily_certification

# JPQL

## CASE

### 기본 CASE 식

```sql
select
	case when m.age <= 10 then '힉생 요금'
			 when m.age >= 60 then '경로 요금'
			 else '일반요금'
	end
from Member m
```

기본적인 if-else 문과 같다. if, else-if대신 when을 쓸 뿐이다.

### 단순 CASE 식

```sql
select
	case t.name 
			 when '팀A' then '인센티브110%'
			 when '팀B' then '인센티브120%'
			 else '인센티브105%'
  end
from Team t
```

switch문과 유사하다. case 대신 when을, default 대신 else를 쓸 뿐이다.

### COALESCE

하나씩 조회해서 null이 아니면 반환

```sql
select coalesce(m.username, '이름 없는 회원') from Member m
```

사용자 이름이 없으면 ‘이름 없는 회원’을 반환 한다.

**JpaMain**

```java
try {
    Team team = new Team();
    team.setName("teamA");
    em.persist(team);

    Member member = new Member();
    member.setUsername("userA");
    member.setAge(10);
    member.setTeam(team);
    member.setType(MemberType.ADMIN);

    em.persist(member);

    em.flush(); //  DB에 반영
    em.clear(); //  영속성 컨텍스트 초기화

    String query = "select coalesce(m.username, '이름 없는 회원') from Member m";

    List<String> result = em.createQuery(query, String.class).getResultList();
    
    for(String s : result) {
      System.out.println("s = " + s);
    }
    
    tx.commit();
  } catch (Exception e) {
    tx.rollback();
    e.printStackTrace();
  } finally {
    em.close();
  }
```

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%201.png)

username이 userA로 존재하므로 userA를 출력한다.

```java
Member member = new Member();
member.setUsername(null);
member.setAge(10);
member.setTeam(team);
member.setType(MemberType.ADMIN);
```

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%202.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%203.png)

username이 null이면 ‘이름 없는 회원’을 출력한다.

### NULLIF

두 값이 같으면 null 반환, 다르면 첫번째 값 반환

```sql
select NULLIF(m.username, '관리자') from Member m
```

사용자 이름이 ‘관리자’면 null을 반환하고 나머지는 본인의 이름을 반환한다.

**JpaMain**

```java
Member member = new Member();
member.setUsername("관리자");
member.setAge(10);
member.setTeam(team);
member.setType(MemberType.ADMIN);
```

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%204.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%205.png)

username이 ‘관리자’이므로 null을 출력한다.

```java
Member member = new Member();
member.setUsername("ABC");
member.setAge(10);
member.setTeam(team);
member.setType(MemberType.ADMIN);
```

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%206.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%207.png)

username이 ‘관리자’가 아니므로 그대로 출력한다.

특정 값을 숨기고 싶을 때 사용할 수 있겠다.

## JPQL 함수

### 기본 함수

- CONCAT
- SUBSTRING
- TRIM
- LOWER, UPPER
- LENGTH
- LOCATE
- ABS, SQRT, MOD
- SIZE, INDEX (JPA 용도)
    - SIZE
        
        **JpaMain**
        
        ```java
        try {
          Team team = new Team();
          team.setName("teamA");
          em.persist(team);
        
          Member member = new Member();
          member.setUsername("  관리자1     ");
          member.setAge(10);
          member.setTeam(team);
          member.setType(MemberType.ADMIN);
        
          em.persist(member);
        
          Member member2 = new Member();
          member2.setUsername("관리자2");
          member2.setAge(20);
          member2.setTeam(team);
          member2.setType(MemberType.ADMIN);
        
          em.persist(member2);
        
          em.flush(); //  DB에 반영
          em.clear(); //  영속성 컨텍스트 초기화
        
          String query = "select size(t.members) from Team t";
        
          List<Integer> result = em.createQuery(query, Integer.class).getResultList();
          
          for(Integer s : result) {
            System.out.println("s = " + s);
          }
          
          tx.commit();
        } catch (Exception e) {
          tx.rollback();
          e.printStackTrace();
        } finally {
          em.close();
        }
        ```
        
        team에 연관된 Member Collections의 크기를 반환한다. team과 연관된 member가 2개이므로 2를 반환한다. 
        

### 사용자 정의 함수

Hibernate에서 사용 전 방언에 추가해야 한다.

사용하려는 DB 방언을 상속받고, 사용자 정의 함수를 등록한다.

```sql
select function('group_concat', i.name) from Item i
```

**MySQL57Dialect.class**

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package org.hibernate.dialect;

import org.hibernate.dialect.function.SQLFunction;
import org.hibernate.dialect.function.StaticPrecisionFspTimestampFunction;

public class MySQL57Dialect extends MySQL55Dialect {
  public MySQL57Dialect() {
    this.registerColumnType(93, "datetime(6)");
    this.registerColumnType(2000, "json");
    SQLFunction currentTimestampFunction = new StaticPrecisionFspTimestampFunction("now", 6);
    this.registerFunction("now", currentTimestampFunction);
    this.registerFunction("current_timestamp", currentTimestampFunction);
    this.registerFunction("localtime", currentTimestampFunction);
    this.registerFunction("localtimestamp", currentTimestampFunction);
    this.registerFunction("sysdate", new StaticPrecisionFspTimestampFunction("sysdate", 6));
  }

  public boolean supportsRowValueConstructorSyntaxInInList() {
    return true;
  }
}

```

이렇게 어느 정도는 등록되어 있지만, 등록되지 않은 함수들은 직접 등록해야  한다.

### 사용자 정의 함수 등록하기

함수를 등록하려는 DB 방언을 상속하는 클래스를 만들어야 한다. H2 DB를 사용하므로 H2Dialect를 상속하는 클래스를 만든다. 작성은 H2Dialect를 참고해서 하면 된다.

**MyH2Dialect.java**

```java
import org.hibernate.dialect.H2Dialect;
import org.hibernate.dialect.function.StandardSQLFunction;
import org.hibernate.type.StandardBasicTypes;

public class MyH2Dialect extends H2Dialect {
  public MyH2Dialect() {
    registerFunction("group_concat", new StandardSQLFunction("group_concat", StandardBasicTypes.STRING));
  }
}

```

persistence.xml에 가서 hibernate.dialect를  MyH2Dialect로 변경해주면 된다.

**persistence.xml**

```xml
<property name="hibernate.dialect" value="dialect.MyH2Dialect"/>
```

**JpaMain**

```java
String query = "select function('group_concat', m.username) from Member m";

List<String> result = em.createQuery(query, String.class).getResultList();

for(String s : result) {
  System.out.println("s = " + s);
}
```

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%208.png)

모든 member의 이름을 이어붙여 출력해준다

## 경로 표현식

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%209.png)

### 상태 필드

```java
String query = "select m.username from Member m";
```

더 이상 탐색이 불가능하다.

### 연관 필드

- **단일 값 연관 필드**
    
    ```java
    String query = "select m.team from Member m";
    ```
    
    ![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2010.png)
    
    묵시적 inner join이 발생한다. 
    
    ```java
    String query = "select m.team.name from Member m";
    ```
    
    ![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2011.png)
    
    team에서도 계속 탐색이 가능하다.
    
    묵시적인 inner join이 발생하므로 가급적 사용을 지양해야 한다. join이 jpql에서도 잘 드러나도록 코딩해야 한다.
    
- **컬렉션 값 연관 필드**
    
    ```java
    String query = "select t.members from Team t";
    
    Collection result = em.createQuery(query, Collection.class).getResultList();
    
    for(Object o : result) {
      System.out.println("o = " + o);
    }
    ```
    
    ![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2012.png)
    
    역시 묵시적인 Inner join이 발생한다.
    
    하지만 중요한 차이가 있는데, members에서는 Collection의 크기를 가져오는 size 외에는 더 이상 탐색이 불가능하다.
    
    ```java
    String query = "select t.members.get(0).username from Team t";
    ```
    
    이딴 건 불가능하다.
    
    만약 정 탐색을 하고 싶다면 from 절에서 명시적 JOIN을 통해 별칭을 얻고, 별칭을 통해 탐색한다.
    
    ```java
    String query = "select m.username from Team t join t.members m";
    ```
    
    ![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2013.png)
    

**하지만 다 필요 없고 그냥 묵시적 JOIN을 사용하지 말자. JOIN은 명시적으로 사용하는 것이 좋다.**

# Problem Solving (Algorithm & SQL)

**BOJ 2220 힙 정렬**

[2220번: 힙 정렬](https://www.acmicpc.net/problem/2220)

말로 설명하기 정말 어렵다. 그래도 그리디하게 생각해보면 힙에서 poll할 때마다 swap 횟수가 최대가 되려면 최솟값 1이 가장 마지막 index에 존재해야 한다는 것을 알 수 있다. 그래야 최댓값을 poll한 이후 최솟값 1이 루트에 위치하게 되어 제 위치를 찾는 과정에서 swap이 최대한 많이 발생한다.

또한 poll 후 1이 가장 마지막 index에 위치해야 다음 poll에서도 swap이 최대한 많이 발생한다.

N = 6일 때 swap이 최대인 최대 힙을 생성하는 과정은 다음과 같다. 핵심은 i는 1부터 n-1까지 순서대로 index i에 i+1을 배치하고 루트로 올리면서 그 경로상에 존재하는 더 작은 값들과 위치를 바꾸는 것이다. 

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2014.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2015.png)

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2016.png)

완성된 최대 힙에서 6을 poll을 해보자. 1이 제 자리를 찾아 움직이는 과정이 6이 루트를 향해 움직이는 경로와 정 반대인 것을 알 수 있다.

![Untitled](24_03_04_daily_certification%201c188f34e51e4c6296105fd3a6fc1903/Untitled%2017.png)

즉 삭제 시 1이 가장 마지막 index로 위치 조정되는 과정의 역과정으로 힙을 만들면 된다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2220 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()

    private var n = 0
    private lateinit var maxHeap : IntArray

    fun solve() {
        n = br.readLine().toInt()

        maxHeap = IntArray(n + 1)

        for(i in 1 ..<n) { //  i + 1 값 위치 정하기
            var p = i

            while(p > 1) {
                maxHeap[p] = maxHeap[p / 2] //  i + 1을 index = 1로 올리면서 이전에 위치했던 값들이랑 자리 바꾸기
                p /= 2
            }

            maxHeap[1] = i + 1  //  최대 힙이므로 index = 1이 현재까지 값 중 최댓값이어야 함
        }

        maxHeap[n] = 1  //  swap이 많으려면 최솟값 1이 삭제 시 index = 1로 와야 swap이 최대가 된다.

        for(i in 1 .. n)
            sb.append("${maxHeap[i]} ")

        print(sb)
    }
}

fun main() {
    BOJ_2220().solve()
}
```