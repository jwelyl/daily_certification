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

  <build>
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
  </build>

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

**BOJ** 

**코드**

```kotlin

```
