# 24_02_06_daily_certification

# JPA

## 고급 매핑

### 상속관계 매핑

- RDB는 객체처럼 상속 관계가 있지는 않다.
- 대신 슈퍼타입-서브타입 관계라는 모델링 기법이 객체의 상속과 유사하다.
- **객체의 상속 구조와 DB의 슈퍼타입-서브타입 관계를 매핑하는 것이 상속관계 매핑이다.**
- 상속관계 매핑에는 조인 전략, 단일 테이블 전략, 구현 클래스마다 테이블 전략 3가지 전략이 있다.

**조인 전략**

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled.png)

하위 객체 각각을 테이블로 구현 후 상위 객체 테이블과 JOIN하여 사용, DTYPE으로 구분

**단일 테이블 전략**

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%201.png)

하나의 테이블에 하위 객체의 속성을 모두 컬럼으로 저장

**구현 클래스마다 테이블 전략**

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%202.png)

하위 객체 각각을 서로 독립적인 테이블로 구현

어떤 방법으로 구현하던 JPA는 매핑 가능하다.

**단일 테이블 전략**

**JPA의 기본 전략은 단일 테이블 전략이다.**

단일 테이블 전략임을 명시하려면 Item에 **@Inheritance(strategy = InheritanceType.SINGLE_TABLE)**로 명시하면 된다.

```java
@Entity
// @Inheritance(strategy = InheritanceType.SINGLE_TABLE)
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

실행시키면 다음과 같은 DDL이 나가며, 다음과 같은 단일 테이블이 생성된다.

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%203.png)

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%204.png)

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%205.png)

단일 테이블 전략 사용 시 insert 쿼리도 1번 나가며 select 쿼리도 JOIN 없이 나가므로 성능 상 이점이 있다.

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%206.png)

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%207.png)

**조인 전략**

조인 전략을 사용하고 싶으면 상위 클래스에 **@Inheritance(strategy = InheritanceType.JOINED)**로 명시하면 된다.

```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED)
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

실행시키면 다음과 같은 DDL이 나가며, 각각이 테이블로 생성된다.

![스크린샷 2024-02-06 01.57.35.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/35.png)

![스크린샷 2024-02-06 01.57.42.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/42.png)

![스크린샷 2024-02-06 01.57.58.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/58.png)

![스크린샷 2024-02-06 01.57.48.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/48.png)

![스크린샷 2024-02-06 01.58.07.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/07.png)

![스크린샷 2024-02-06 01.58.12.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/12.png)

![스크린샷 2024-02-06 01.58.21.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/21.png)

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%208.png)

**JpaMain insert**

```java
try {
  Movie movie = new Movie();
  movie.setDirector("AAA");
  movie.setActor("BBB");
  movie.setName("바람과 함께 사라지다.");
  movie.setPrice(10000);

  em.persist(movie);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

**Query**

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%209.png)

부모 테이블 ITEM과 자식 테이블  MOVIE에 모두 insert 쿼리가 나간다.

**Result**

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2010.png)

부모 테이블(ITEM)의 PK가 자식 테이블(MOVIE)의 PK이자 FK이다.

**JpaMain select**

```java
try {
  Movie movie = new Movie();
  movie.setDirector("AAA");
  movie.setActor("BBB");
  movie.setName("바람과 함께 사라지다.");
  movie.setPrice(10000);

  em.persist(movie);

  em.flush(); //  쿼리 DB로 보내고
  em.clear(); //  영속성 컨텍스트 초기화해서 1차 캐시 비우기(쿼리 확인)

  Movie findMovie = em.find(Movie.class, movie.getId());
  System.out.println("findMovie = " + findMovie);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

자식 테이블을 조회할 경우 자식 테이블과 부모 테이블의 INNER JOIN을 통해 결과를 가져온다.

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2011.png)

자식 테이블을 구분하는 DTYPE이 필요할 경우 부모 테이블에 **@DiscriminatorColumn**을 사용한다.

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

ITEM 테이블을 생성할 때 DTYPE 컬럼이 추가된 것을 알 수 있고,

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2012.png)

ITEM 테이블에 insert된 row에도 Entity 이름이 DTYPE 값이 들어간 것을 알 수 있다.

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2013.png)

DTYPE을 넣어주는 것이 쿼리를 추적하는등 운영에 용이하다. 특히 단일 테이블 전략에서 더욱 중요하다. (단일 테이블 전략에서는 @DiscriminatorColumn이 없어도 DTYPE을 넣어준다.)

DTYPE 명을 바꾸고 싶으면 자식 Entity에서 **@DiscriminatorValue(”원하는 이름”)**으로 변경하면 된다. 가급적 관례를 따르자.

**구현 클래스마다 테이블 전략**

구현 클래스마다 테이블 전략을 사용하고 싶으면 

상위 클래스에 **@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)**로 명시하면 된다. 

이 때 상위 클래스는 추상 클래스로 만든다.

```java
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

이 경우 ITEM 테이블은 만들어지지 않고, ALBUM, BOOK, MOVIE 테이블에 ITEM 테이블의 속성들이 중복해서 들어가게 된다.

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2014.png)

@DiscriminatorColumn을 넣어줘도  DTYPE이 의미가 없어서 아무 일도 생기지 않는다.

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2015.png)

insert, select 모두 간단하다. 매우 좋아보인다.

하지만 치명적인 단점이 존재한다.

**JpaMain select**

```java
try {
  Movie movie = new Movie();
  movie.setDirector("AAA");
  movie.setActor("BBB");
  movie.setName("바람과 함께 사라지다.");
  movie.setPrice(10000);

  em.persist(movie);

  em.flush(); //  쿼리 DB로 보내고
  em.clear(); //  영속성 컨텍스트 초기화해서 1차 캐시 비우기(쿼리 확인)

  Item findItem = em.find(Item.class, movie.getId());
  System.out.println("findItem = " + findItem);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
} finally {
  em.close();
}
```

만약 상위 타입 Item으로 검색할 경우, union all로 하위 타입 테이블을 다 검색해야 해서 아래와 같은 끔찍한 쿼리가 생성된다.

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

매우 비효율적으로 동작하게 된다.

## 조인 전략 vs 단일 테이블 전략 vs TABLE_PER_CLASS 전략

일단 기본적으로는 조인 전략을 사용하는 것이 좋다. JOIN을 잘 설계하면 생각만큼 성능 저하가 심하지 않다.

확실한 것은 TABLE_PER_CLASS 전략은 쓸 일이 없다. ORM 전문가, DBA 모두에게 외면받았다.

**기본적으로는 조인 전략을 사용하되, 정말 정말 단순할 경우에는 단일 테이블 전략을 사용하자.**
