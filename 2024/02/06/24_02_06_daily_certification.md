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

![35.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/35.png)

![42.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/42.png)

![58.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/58.png)

![48.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/48.png)

![07.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/07.png)

![12.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/12.png)

![21.png](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/21.png)

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

### @MappedSuperclass

**공통 매핑 정보가 필요할 때 사용**

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2016.png)

**ex) BaseTimeEntity**

애플리케이션에는 대부분 누가 몇시에 등록했고, 누가 몇시에 수정했는지의 정보가 필요하다.

```java
private String createdBy;
private LocalDateTime createdDate;
private String updatedBy;
private LocalDateTime updatedDate;
```

위 속성을 기존에 존재하는 모든 Entity에 추가하는 것은 매우 번거롭다.

그런 번거로움을 해결하기 위해 사용한다.

공통되는 속성을 BaseEntity에 정의한다. **@MappedSuperclass**는 매핑 정보만 받는 부모 클래스임을 나타낸다.

**BaseEntity**

```java
@MappedSuperclass
public abstract class BaseEntity {
  private String createdBy;
  private LocalDateTime createdDate;
  private String updatedBy;
  private LocalDateTime updatedDate;

  /* getter, setter */
}
```

그리고 BaseEntity의 속성을 필요로 하는 Entity에서 BaseEntity를 상속한다.

**Team Entity**

```java
@Entity
public class Team extends BaseEntity {}
```

**JpaMain**

```java
try {
  Member member = new Member();
  member.setCreatedBy("kim");
  member.setCreatedDate(LocalDateTime.now());
  
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

![Untitled](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/Untitled%2017.png)

**@MappedSuperclass**는 상속 관계 매핑이 아니다.

BaseEntity는 Entity가 아니다. 따라서 테이블과 매핑도 안된다. 단지 매핑 정보만 제공한다.

BaseEntity로는 조회가 되지 않는다. Item 타입으로 Album, Book, Movie가 조회됐던 것과 다르게 BaseEntity로는 Member, Team을 조회할 수 없다.

**BaseEntity를 직접 생성할 일이 없으므로 abstract class로 만들자.**

# Problem Solving (Algorithm & SQL)

**BOJ 7453 합이 0인 네 정수**

[7453번: 합이 0인 네 정수](https://www.acmicpc.net/problem/7453)

Bruteforcing으로 해결하려 하면 O(N^4)로 시간초과를 피할 수 없다.

A, B 배열에서  각각 하나씩 뽑아 더한 값과, C, D 배열에서 각각 하나씩 뽑아 더한 값을 각각 AB, CD 배열에 저장한다. 각각 O(N^2)의 시간복잡도를 가진다.

AB의 원소 num에 대하여 CD에서 -num의 개수를 찾는다. 이분탐색을 위해 CD를 정렬하고 N^2번 이분 탐색을 해야 하므로 전체 시간복잡도는 O(N^2logN)이 된다.

주의할 사항으로 CD를 ArrayList로 저장해서는 안된다. ArrayList 자체가 배열보다 Overhead가 크다.

또한 정렬 시 Arrays.sort를 사용해야 한다.

[[Java] Arrays.sort()와 Collections.sort()에 대해서 자세히 알아보자!](https://codingnojam.tistory.com/38)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_7453 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    배열 크기
    private lateinit var arrA : IntArray   //    배열 A
    private lateinit var arrB : IntArray    //    배열 B
    private lateinit var arrC : IntArray    //    배열 C
    private lateinit var arrD : IntArray    //    배열 D

    private lateinit var arrCD : IntArray    //    배열 C, D에서 각각 수 1개씩 뽑아서 더한 수들 저장 배열

    private var ans = 0L    //    A[a] + B[b] + C[c] + D[d] = 0인 (a, b, c, d) 개수

    fun solve() {
        n = br.readLine().toInt()

        arrA = IntArray(n)
        arrB = IntArray(n)
        arrC = IntArray(n)
        arrD = IntArray(n)

        repeat(n) {
            tokens = StringTokenizer(br.readLine())

            arrA[it] = tokens.nextToken().toInt()
            arrB[it] = tokens.nextToken().toInt()
            arrC[it] = tokens.nextToken().toInt()
            arrD[it] = tokens.nextToken().toInt()
        }

        arrCD = IntArray(n * n)

        var idx = 0

        for(c in 0 ..< n) {
            for(d in 0 ..< n)
                arrCD[idx++] = arrC[c] + arrD[d]    //    C[c] + D[d] 저장
        }

        arrCD.sort()    //    이분 탐색을 위한 정렬

        for(a in 0 ..< n) {
            for(b in 0 ..< n)
                ans += binarySearch(-arrA[a] - arrB[b])    //    A[a] + B[b]와 합이 0인 C[c] + D[d] 개수 찾아서 반환
        }

        println(ans)
    }

    //    arrCD에서 target의 개수 찾아서 반환
    private fun binarySearch(target : Int) : Int {
        var start = 0
        var end = n * n - 1

        var lowerBound = -1    //    target의 가장 작은 index
        var upperBound = -1    //    target의 가장 큰 index

        //    lowerBound 찾기
        while(start <= end) {
            val mid = (start + end) / 2

            if(arrCD[mid] == target) {    //    찾은 경우
                lowerBound = mid    //    일단 저장
                end = mid - 1    //    더 왼쪽에서 찾아보기
            }
            else if(arrCD[mid] < target)    //    더 작은 경우
                start = mid + 1    //    더 오른쪽에서 찾아보기
            else                            //    더 큰 경우
                end = mid - 1    //   더 왼쪽에서 찾아보기
        }

        if(lowerBound == -1)    //    target이 없을 경우
            return 0

        start = 0
        end = n * n - 1

        //    upperBound 찾기
        while(start <= end) {
            val mid = (start + end) / 2

            if(arrCD[mid] == target) {    //    찾은 경우
                upperBound = mid    //    일단 저장
                start = mid + 1    //    더 오른쪽에서 찾아보기
            }
            else if(arrCD[mid] < target)    //    더 작은 경우
                start = mid + 1    //    더 오른쪽에서 찾아보기
            else                            //    더 큰 경우
                end = mid - 1    //   더 왼쪽에서 찾아보기
        }

        return upperBound - lowerBound + 1
    }
}

fun main() {
    BOJ_7453().solve()
}
```

**코딩테스트 연습 > JOIN > 조건에 맞는 도서와 저자 리스트 출력하기**

[](https://school.programmers.co.kr/learn/courses/30/lessons/144854)

BOOK과 author_id로 join한 후 그 중 

![join.jpeg](24_02_06_daily_certification%200466251f48124fedbf5f21bf6174d190/join.jpeg)

**BOOK 테이블**

```sql
select 
    b.book_id as book_id, b.category as category, b.author_id as author_id, 
    b.price as price, date_format(b.published_date, '%Y-%m-%d') as published_date
from BOOK b;
```

| book_id | category | author_id | price | published_date |
| --- | --- | --- | --- | --- |
| 1 | 경제 | 1 | 9000 | 2020-01-10 |
| 2 | 경제 | 1 | 12000 | 2021-06-10 |
| 3 | 인문 | 1 | 11000 | 2021-10-24 |
| 4 | 소설 | 2 | 7500 | 2020-03-03 |
| 5 | 기술 | 3 | 11000 | 2020-02-17 |
| 6 | 기술 | 3 | 8000 | 2020-04-29 |
| 7 | 생활 | 3 | 9500 | 2021-08-20 |

**AUTHOR 테이블**

```sql
select 
    a.author_id as author_id, 
    a.author_name as author_name
from AUTHOR a;
```

| author_id | author_name |
| --- | --- |
| 1 | 홍길동 |
| 2 | 김영호 |
| 3 | 김수진 |

**BOOK, AUTHOR inner join 테이블**

```sql
select
    b.book_id as book_id,
    b.category as category,
    b.author_id as author_id,
    b.price as price,
    date_format(b.published_date, '%Y-%m-%d') as published_date,
    a.author_id as author_id,
    a.author_name as author_name
from BOOK b inner join AUTHOR a
on b.author_Id = a.author_id;
```

| book_id | category | author_id | price | published_date | author_id | author_name |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 경제 | 1 | 9000 | 2020-01-10 | 1 | 홍길동 |
| 2 | 경제 | 1 | 12000 | 2021-06-10 | 1 | 홍길동 |
| 3 | 인문 | 1 | 11000 | 2021-10-24 | 1 | 홍길동 |
| 4 | 소설 | 2 | 7500 | 2020-03-03 | 2 | 김영호 |
| 5 | 기술 | 3 | 11000 | 2020-02-17 | 3 | 김수진 |
| 6 | 기술 | 3 | 8000 | 2020-04-29 | 3 | 김수진 |
| 7 | 생활 | 3 | 9500 | 2021-08-20 | 3 | 김수진 |

정답

```sql
select
    b.book_id as book_id,
    a.author_name as author_name,
    date_format(b.published_date, '%Y-%m-%d') as published_date
from BOOK b inner join AUTHOR a
on b.author_id = a.author_id
where b.category = '경제'
order by published_date;
```

| book_id | author_name | published_date |
| --- | --- | --- |
| 1 | 홍길동 | 2020-01-10 |
| 2 | 홍길동 | 2021-06-10 |