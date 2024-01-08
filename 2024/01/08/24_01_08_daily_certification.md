# 24_01_08_daily_certification

# JPA

## Project Setting

**h2 database**

![Untitled](24_01_08_daily_certification%20eec62c847c0f4cec8f4d17ff8989e546/Untitled.png)

최초에는 JDBC URL에 **jdbc:h2:~/test** 입력 후 연결한다. 그 이후 위 이미지의 URL로 연결하면 된다.

**pom.xml**

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.example</groupId>
  <artifactId>ex1-hello-jpa</artifactId>
  <version>1.0-SNAPSHOT</version>
  <name>Archetype - ex1-hello-jpa</name>
  <url>http://maven.apache.org</url>

  **<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>
    </plugins>
  </build>**

  <dependencies>
    <!-- JPA 하이버네이트 -->
    <dependency>
      <groupId>org.hibernate</groupId>
      <artifactId>hibernate-entitymanager</artifactId>
      <version>5.3.10.Final</version>
    </dependency>

    <!-- H2 데이터베이스 -->
    <dependency>
      <groupId>com.h2database</groupId>
      <artifactId>h2</artifactId>
      <version>2.2.224</version>
    </dependency>
  </dependencies>
</project>
```

Java 8을 사용할 경우 build 태그 내용을 추가해줘야 한다.

**persistence.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
</persistence>
```

main/resource/META-INF에 persistence.xml을 추가해야 한다.

## JPA 구동 방식

![Untitled](24_01_08_daily_certification%20eec62c847c0f4cec8f4d17ff8989e546/Untitled%201.png)

## JPA 개발

### Entity Manager Factory

```java
public class JpaMain {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
	  // ...
		emf.close();
  }
}
```

EntityManagerFactory는 애플리케이션 전체에서 하나만 생성해서 공유한다.

### Entity Manager

```java
public class JpaMain {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

    EntityManager em = emf.createEntityManager();
		//  DB 접근 코드
    em.close();

    emf.close();
  }
}
```

Entity Manager는 쓰레드 간에 공유할 수 없다. 

### Transaction

```java
public class JpaMain {
  public static void main(String[] args) {
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

    EntityManager em = emf.createEntityManager();

    EntityTransaction tx = em.getTransaction();

		tx.begin();  // 트랜잭션 시작
		try {
			// DB 변경 코드
			tx.commit();   // 변경 성공 시 commit
		} catch(Exception e) {
			tx.rollback(); // 변경 실패 시 rollback
		} finally {
			em.close();;  //  entity manager는 DB connection 물고 있으므로 사용 끝나면 닫아줘야 함
		}

    emf.close();
  }
}
```

JPA의 모든 데이터 변경은 트랜잭션 안에서 실행되어야 한다.

### JPQL

```java
//  JPQL
tx.begin();

try {
  List<Member> result = em.createQuery("select m from Member as m", Member.class)
      .getResultList();

  for(Member member : result) {
    System.out.println("member.name = " + member.getName());
  }

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

# Problem Solving (Algorithm & SQL)

**BOJ 13016 내 왼손에는 흑염룡이 잠들어 있다**

[13016번: 내 왼손에는 흑염룡이 잠들어 있다](https://www.acmicpc.net/problem/13016)

트리의 지름 응용 문제다.

트리에서 임의의 정점에서 가장 먼 정점은 해당 트리의 지름의 양 끝 점 중 하나이다.

트리의 지름을 구하는 과정에서 DFS 두 번, 구한 양 끝 정점에서 다른 정점까지의 거리를 구하기 위한 DFS 각각 1번, 총 4번의 DFS로 문제를 해결할 수 있다.

브루트포스로 해결할 수 없음은 쉽게 파악 가능하므로 트리의 지름 또는 트리 DP를 활용하는 방법을 생각해봐야 한다.

트리 DP로도 해결해봐야겠다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_13016 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0
    private lateinit var tree : Array<MutableList<Node>>
    private lateinit var visited : BooleanArray
    private var diameter = 0
    private var v1 = 1  //  지름의 한쪽 끝 점
    private var v2 = 1  //  지름의 다른 한쪽 끝 점
    private lateinit var dist1 : IntArray   //  v1에서 각 정점까지의 거리
    private lateinit var dist2 : IntArray   //  v2에서 각 정점까지의 거리

    fun solve() {
        n = br.readLine().toInt()

        tree = Array(n + 1) { mutableListOf() }
        visited = BooleanArray(n + 1)
        dist1 = IntArray(n + 1)
        dist2 = IntArray(n + 1)

        repeat(n - 1) {
            tokens = StringTokenizer(br.readLine())

            val v1 = tokens.nextToken().toInt()
            val v2 = tokens.nextToken().toInt()
            val dist = tokens.nextToken().toInt()

            tree[v1].add(Node(v2, dist))
            tree[v2].add(Node(v1, dist))
        }

        dfs(v1, 0)
        v1 = v2
        diameter = 0
        Arrays.fill(visited, false)
        dfs(v1, 0)

        findDist(v1, 0, dist1)
        findDist(v2, 0, dist2)

        for(i in 1 .. n)
            sb.append("${dist1[i].coerceAtLeast(dist2[i])}\n")

        print(sb)
    }

    private fun findDist(v : Int, dist : Int, distArr : IntArray) {
        distArr[v] = dist

        for(next in tree[v]) {
            val nv = next.v
            val ndist = next.dist

            if(distArr[nv] == 0)
                findDist(nv, dist + ndist, distArr)
        }
    }

    private fun dfs(v : Int, dist : Int) {
        visited[v] = true

        if(diameter < dist) {
            diameter = dist
            v2 = v
        }

        for(next in tree[v]) {
            val nv = next.v
            val ndist = next.dist

            if(!visited[nv])
                dfs(nv, dist + ndist)
        }
    }

    private class Node(val v : Int, val dist : Int)
}

fun main() {
    BOJ_13016().solve()
}
```