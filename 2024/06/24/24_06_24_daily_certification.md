# 24_06_24_daily_certification

```
[koreii] #176 데일리인증
2024624
JPA
- 상속 관계 매핑 복습
- 단일 테이블
- 조인 전략
- 구현 클래스 테이블
알고리즘 & 코딩 테스트 대비
- Greedy, 기타 잡다한 문제
```

# JPA

# 상속 관계 매핑

## 단일 테이블 전략 (Default)

![Untitled](24_06_24_daily_certification%20f8af7131b77449fdb439d6c23502e364/Untitled.png)

부모 테이블의 공통적인 column과, 자식 테이블 각각의 고유한 column을 부모 테이블 하나에 통합시켜 모두 포함시키는 방법

구분 컬럼(DTYPE)을 통해 실제 어떤 자식 테이블의 정보를 저장하는지 구분한다. 

**Entity**

```java
//  부모 엔티티
@Entity
// @Inheritance(strategy = InheritanceType.SINGLE_TABLE)
public class Item {
  @Id
  @GeneratedValue
  private Long id;

  private String name;
  private int price;
}

//  자식 엔티티
@Entity
public class Album extends Item {
  private String artist;
}

@Entity
public class Book extends Item {
  private String author;
  private String isbn;
}

@Entity
public class Movie extends Item {
  private String director;
  private String actor;
}
```

**DDL**

```sql
create table Item (
	DTYPE varchar(31) not null,
	id bigint not null,
	name varchar(255),
	price integer not null,
	artist varchar(255),
	author varchar(255),
	isbn varchar(255),
	actor varchar(255),
	director varchar(255),
	primary key (id)
);
```

![Untitled](24_06_24_daily_certification%20f8af7131b77449fdb439d6c23502e364/Untitled%201.png)

### 단일 테이블 전략 장단점

**장점**

- JOIN이 필요 없으므로 일반적으로 조회 성능이 빠르다.
- JOIN이 필요 없으므로 select 쿼리가 단순하다.

**단점**

- 자식 엔티티가 매핑한 컬럼은 모두 nullable해야 한다.
- 단일 테이블에 모든 정보를 저장해서 테이블이 커질 수 있다. 상황에 따라 조회 성능이 더 느려질 수 있다.
    - 테이블이 커지면 조회 성능이 느려지는 이유?

## 조인 전략

![Untitled](24_06_24_daily_certification%20f8af7131b77449fdb439d6c23502e364/Untitled%202.png)

부모 엔티티와 자식 엔티티 모두 테이블로 만들고, 자식 테이블이 부모 테이블의 PK를 PK이자 FK로 가진다.

조회할 때 자식 테이블의 FK와 부모 테이블의 PK로 조인을 해야 한다.

부모 테이블의 DTYPE 컬럼으로 참조하는 자식 테이블을 구분 가능하다.

(DTYPE 컬럼은 @DisciriminatorColumn 어노테이션을 추가해야 생성된다. JPA 표준은 사용하는 것이므로 사용하도록 하자.)

**Entity**

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
@DiscriminatorColumn
public class Item {
  @Id
  @GeneratedValue
  private Long id;

  private String name;
  private int price;
}

@Entity
public class Album extends Item {
  private String artist;
}

@Entity
public class Book extends Item {
  private String author;
  private String isbn;
}

@Entity
public class Movie extends Item {
  private String director;
  private String actor;
}
```

**DDL**

```sql
create table Item (
	DTYPE varchar(31) not null,
	id bigint not null,
	name varchar(255),
	price integer not null,
	primary key (id)
);

create table Album (
	artist varchar(255),
	id bigint not null,
	primary key (id)
);
	
create table Book (
	author varchar(255),
	isbn varchar(255),
	id bigint not null,
	primary key (id)
);
	
create table Movie (
	actor varchar(255),
	director varchar(255),
	id bigint not null,
	primary key (id)
);

alter table Album
	add constraint FK2casdwqedewrwqe24332
	foreign key (id)
	reference Item;

alter table Book
	add constraint FKac23s434qe45334esdfrw
	foreign key (id)
	reference Item;

alter table Movie
	add constraint FKc32dxwqe3emnwqe9e3dqc
	foreign key (id)
	reference Item;
```

![Untitled](24_06_24_daily_certification%20f8af7131b77449fdb439d6c23502e364/Untitled%203.png)

![Untitled](24_06_24_daily_certification%20f8af7131b77449fdb439d6c23502e364/1ea484ee-5bc6-4a83-b709-143117dc7bd4.png)

### 조인 전략 장단점

**장점**

- 테이블이 정규화되어 Nullable한 컬럼이 사라진다.
- Foreign key referential integrity constraint를 사용할 수 있다.
- 저장 공간을 효율적으로 사용한다.
- 자식 엔티티가 매핑한 컬럼은 모두 nullable해야 한다.

**단점**

- 조회할 때 JOIN이 많이 사용되므로 성능이 저하된다.
- JOIN을 사용하므로 조회 쿼리가 복잡해진다.
- 데이터를 저장할 때 부모 테이블과 자식 테이블에 모두 저장해야 하므로 INSERT SQL을 두 번 실행한다.

## 구현 클래스 테이블 전략 (비추천)

![Untitled.png](24_06_24_daily_certification%20f8af7131b77449fdb439d6c23502e364/Untitled%204.png)

자식 엔티티마다 각각 테이블을 만들고, 부모 테이블의 공통된 컬럼을 포함한 모든 컬럼을 각각의 테이블이 가지고 있도록 구현한다. 부모 엔티티의 테이블은 생성되지 않는다.

**Entity**

```java
//  부모 엔티티 - 추상 클래스
@Entity
@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)
@DiscriminatorColumn
public abstract class Item {
  @Id
  @GeneratedValue
  private Long id;

  private String name;
  private int price;
}

//  자식 엔티티
@Entity
public class Album extends Item {
  private String artist;
}

@Entity
public class Book extends Item {
  private String author;
  private String isbn;
}

@Entity
public class Movie extends Item {
  private String director;
  private String actor;
}
```

**DDL**

```sql
create table Album (
	id bigint not null,
	name varchar(255),
	price integer not null,
	artist varchar(255),
	primary key (id)
);
	
create table Book (
	id bigint not null,
	name varchar(255),
	price integer not null,
	author varchar(255),
	isbn varchar(255),
	primary key (id)
);
	
create table Movie (
	id bigint not null,
	name varchar(255),
	price integer not null,
	actor varchar(255),
	director varchar(255),
	primary key (id)
);
```

### 구현 클래스 테이블 전략 장단점

**장점**

- 하위 타입을 구분하여 처리할 때 효과적이다.
    - Item 테이블 하나에 불필요한 데이터를 삽입, 조회할 필요도 없고, JOIN도 필요없다.
- not null 제약 조건을 사용할 수 있다.

**치명적인 단점**

- 하위 타입으로 조회하는 것이 아닌 상위 타입으로 조회할 때 성능이 매우 느리다
    - 모든 하위 타입 테이블을 조회한 후 union all로 결과물을 합해야 한다.
- 자식 테이블을 통합해서 처리하기 어렵다.

상위 타입(Item) 객체로 조회 시 select 쿼리

```sql
Hibernate: 
    select
        item0_.id as id1_2_0_,
        item0_.name as name2_2_0_,
        item0_.price as price3_2_0_,
        item0_.artist as artist1_0_0_,
        item0_.author as author1_1_0_,
        item0_.isbn as isbn2_1_0_,
        item0_.actor as actor1_6_0_,
        item0_.director as director2_6_0_,
        item0_.clazz_ as clazz_0_ 
    from
        ( select
            id,
            name,
            price,
            artist,
            null as author,
            null as isbn,
            null as actor,
            null as director,
            1 as clazz_ 
        from
            Album 
        union
        all select
            id,
            name,
            price,
            null as artist,
            author,
            isbn,
            null as actor,
            null as director,
            2 as clazz_ 
        from
            Book 
        union
        all select
            id,
            name,
            price,
            null as artist,
            null as author,
            null as isbn,
            actor,
            director,
            3 as clazz_ 
        from
            Movie 
    ) item0_ 
where
    item0_.id=?
```

## 조인 전략 vs 단일 테이블 전략 vs TABLE_PER_CLASS 전략

일단 기본적으로는 조인 전략을 사용하는 것이 좋다. JOIN을 잘 설계하면 생각만큼 성능 저하가 심하지 않다.

확실한 것은 TABLE_PER_CLASS 전략은 쓸 일이 없다. ORM 전문가, DBA 모두에게 외면받았다.

**기본적으로는 조인 전략을 사용하되, 정말 정말 단순할 경우에는 단일 테이블 전략을 사용하자.**

# Problem Solving (Algorithm & SQL)

### BOJ 30805 **사전 순 최대 공통 부분 수열**

[](https://www.acmicpc.net/problem/30805)

```kotlin
import java.util.*

fun main() {
    val br = System.`in`.bufferedReader()
    val out = System.out.bufferedWriter()

    val n = br.readLine().toInt()
    val a = br.readLine().split(" ").map { it.toInt() }.toMutableList()

    val m = br.readLine().toInt()
    val b = br.readLine().split(" ").map { it.toInt() }.toMutableList()

    val ans = mutableListOf<Int>()
    var flag = true

    while (true) {
        while (true) {
            if (a.isEmpty() || b.isEmpty()) {
                flag = false
                break
            }

            val maxA = a.maxOrNull() ?: break
            val aIdx = a.indexOf(maxA)
            val maxB = b.maxOrNull() ?: break
            val bIdx = b.indexOf(maxB)

            if (maxA == maxB) break
            else if (maxA > maxB) a.removeAt(aIdx)
            else b.removeAt(bIdx)
        }

        if (!flag) break

        val maxA = a.maxOrNull() ?: break
        ans.add(maxA)

        a.subList(0, a.indexOf(maxA) + 1).clear()
        b.subList(0, b.indexOf(maxA) + 1).clear()
    }

    out.write("${ans.size}\n")
    if (ans.isNotEmpty()) {
        out.write(ans.joinToString(" "))
    }
    out.flush()
}
```