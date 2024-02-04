# 24_02_05_daily_certification

# JPA

## 연관관계 매핑

### 일대일 [1:1] 단방향

**주 테이블에 FK를 가지는 단방향**

![Untitled](24_02_05_daily_certification%207d826029519e41f19d845c3441e43690/Untitled.png)

### 일대일 [1:1] 양방향

**FK를 관리하는 쪽이 연관관계의 주인이다.**

![Untitled](24_02_05_daily_certification%207d826029519e41f19d845c3441e43690/Untitled%201.png)

Member에서 locker 참조로 FK를 관리하므로 locker를 통해서 FK 삽입, 수정이 가능하다.

**Locker Entity**

```java
@Entity
public class Locker {
  @Id
  @GeneratedValue
  private Long id;

  private String name;
}
```

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

  @OneToOne
  @JoinColumn(name = "LOCKER_ID")
  private Locker locker;

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

**@OneToOne** 어노테이션으로 Member에서 Locker를 참조하는 일대일 단방향 매핑을 구현할 수 있다.

**Locker Entity**

```java
@Entity
public class Locker {
  @Id
  @GeneratedValue
  private Long id;

  private String name;

	@OneToOne(mappedBy = "locker")
  private Member member;
}
```

일대일 양방향 매핑을 구현하고 싶으면 Locker에도 Member의 참조를 @OneToOne 어노테이션과 함께 넣으면 된다. 이 때 연관관계의 주인은 Member의 locker 참조이므로, Locker의 member 참조는 locker 참조와 mappedBy로 매핑시킨다.

JPA에서는 일대다 단방향 매핑도 지원했었다. 즉 대상 테이블에 FK가 있는 것을 관리할 수는 있었다. 하지만 일대일에서는 대상 테이블에 FK가 존재하는 단방향 매핑은 지원하지 않는다.

### 다대다 [N:M]

**관계형 데이터베이스에서는 정규화된 2개의 테이블로 다대다 관계를 표현할 수 없다.**

**연결 테이블(Join Table)을 추가해서 일대다, 다대일 관계로 풀어내야 한다.**

![Untitled](24_02_05_daily_certification%207d826029519e41f19d845c3441e43690/Untitled%202.png)

객체는 Collections를 사용해서 객체 2개로 다대다 관계를 구현할 수 있긴 하다.

ex) Member는 List<Product>를, Product는 List<Member>를 가짐

Member에서 Product로 다대다 단방향 매핑을 구현해보자.

**Product Entity**

```java
@Entity
public class Product {
  @Id
  @GeneratedValue
  private Long id;

  private String name;
}
```

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

  @OneToOne
  @JoinColumn(name = "LOCKER_ID")
  private Locker locker;

  @ManyToMany
  @JoinTable(name = "MEMBER_PRODUCT")
  private List<Product> products = new ArrayList<>();

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

Member에 **@ManyToMany** 어노테이션과 함께 Product Collection을 만들면 된다. **@JoinTable** 어노테이션에 연결 테이블 이름을 명시하면 해당 이름으로 연결 테이블이 생성된다.

다대다 양방향 매핑은 Product에 Member Collections을 만들고 @ManyToMany mappedBy로 매핑시키면 된다.

**Product Entity**

```java
@Entity
public class Product {
  @Id
  @GeneratedValue
  private Long id;

  private String name;

  @ManyToMany(mappedBy = "products")
  private List<Member> members = new ArrayList<>();
}
```

### 다대다 매핑을 사용하지 않는 이유

중간 테이블이 단순히 두 테이블의 PK만 저장하여 연결하는 용도로만 사용되고 끝이 아닌 경우가 많다.

주문 시간, 수량 같은 추가 데이터가 같이 필요한 경우가 많다.

### 다대다 매핑을 다대일 + 일대다 로 변경

중간 테이블 역할을 할 Entity를 직접 만든 후 Member, Product와 각각 다대일, 일대다로 매핑해준다.

**MemberProduct Entity**

```java
@Entity
public class MemberProduct {
  @Id
  @GeneratedValue
  private Long id;

  @ManyToOne
  private Member member;

  @ManyToOne
  private Product product;
}
```

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

  @OneToOne
  @JoinColumn(name = "LOCKER_ID")
  private Locker locker;

  @OneToMany(mappedBy = "member")
  private List<MemberProduct> memberProducts = new ArrayList<>();

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

**Product Entity**

```java
package hellojpa;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.ManyToMany;
import javax.persistence.OneToMany;
import java.util.ArrayList;
import java.util.List;

@Entity
public class Product {
  @Id
  @GeneratedValue
  private Long id;

  private String name;

  @OneToMany(mappedBy = "product")
  private List<MemberProduct> memberProducts = new ArrayList<>();
}
```

![Untitled](24_02_05_daily_certification%207d826029519e41f19d845c3441e43690/Untitled%203.png)

중간 테이블의 경우 두 테이블의 PK의 조합으로 PK를 만들수도 있지만 가급적이면 중간 테이블만의 PK를 따로 만드는 것이 좋다.

# Problem Solving (Algorithm & SQL)

**BOJ 13202 피자 배치**

[13202번: 피자 배치](https://www.acmicpc.net/problem/13202)

**코드**

```kotlin

```