# 24_02_09_daily_certification

```
[koreii] #40 데일리인증
20240209
JPA
- JPA 값 타입
- 임베디드 타입

알고리즘
- 문자열 파싱 문제 풀이 + 난이도 기여
```

# JPA

## JPA 데이터 타입

### Entity 타입

- **@Entity로 정의하는 객체**
- Entity 객체 내부 데이터가 변해도 식별자로 지속해서 추적 가능

### 값 타입

- int, Integer, String 같은 단순 값으로 사용하는 Java primitive 또는 reference 타입
- 식별자가 없고 값만 존재하므로 변경 시 추적할 수 없다.

## 값 타입

### 기본값 타입

Java에서 기본적으로 정의

- Java Primitive Type (int, double, …)
- Wrapper class (Integer, Double)
- String

### 임베디드 타입 (embedded type, 복합 값 타입)

JPA에서 정의해서 사용해야 함

### 컬렉션 값 타입(collection value type)

JPA에서 정의해서 사용해야 함

### 기본값 타입

- Entity에 생명주기 의존 ex) 회원 Entity 제거 시 내부 field 값도 삭제됨
- 값 타입은 공유되서는 안된다. ex) 회원 member1의 이름 변경 시 다른 회원 member2의 이름도 같이 변경되서는 안됨

primitive 타입은 값을 절대 공유하지 않고 값을 복사해서 사용한다.

```java
public class ValueMain {
  public static void main(String[] args) {
    int a = 10;
    int b = a;

    System.out.println("a = " + a);
    System.out.println("b = " + b);

    a = 20;

    System.out.println("a = " + a);
    System.out.println("b = " + b);
  }
}
```

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled.png)

Integer, Double 같은 Wrapper 클래스나 String은 reference로 공유는 가능하지만 변경이 되지는 않는다.

### 임베디드 타입

새로운 값 타입을 직접 정의할 수 있다.

주로 기본 값 타입들을 모아서 만들지만 결국 int, String과 같은 값 타입이다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%201.png)

Member Entity의 startDate와 endDate는 묶어서 period로 관리하는 것이 자연스럽다. 또한 city, street, zipcode도 묶어서 address로 관리하는 것이 자연스럽다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%202.png)

startDate와 endDate를 묶어 Period 클래스로,  city, street, zipcode를 묶어서 Address 클래스로 만들어 관리할 수 있다. Period와 Address가 임베디드타입이다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%203.png)

JPA에서 매핑하여 사용한다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%204.png)

이를 매핑하려면 어떻게 해야 할까?

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

	// Period
  private LocalDateTime startDate;
  private LocalDateTime endDate;

	// Address
  private String city;
  private String street;
  private String zipcode;

	/* getter, setter */
}
```

기본값 타입만을 이용해 Member Entity를 정의하면 위와 같다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%205.png)

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%206.png)

Member를 embedded 값 타입을 사용하도록 변경해주자.

**Period**

```java
@Embeddable
public class Period {
  private LocalDateTime startDate;
  private LocalDateTime endDate;

	/* default constructor, getter, setter*/
}
```

**Address**

```java
@Embeddable
public class Address {
  private String city;
  private String street;
  private String zipcode;

	/* default constructor, getter, setter*/
}
```

값 타입을 정의할 때는 **@Embeddable**을 붙여준다.

getter, setter, 그리고 **기본 생성자는 반드시 있어야 한다.** 기본 생성자가 없으면 컴파일되지 않는다.

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

  // Period
  @Embedded
  private Period workPeriod;

  // Address
  @Embedded
  private Address homeAddress;

  public Long getId() {
    return id;
  }

  /* getter, setter */
}
```

값 타입을 사용하는 곳에는 **@Embedded** 어노테이션을 붙여준다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%205.png)

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%206.png)

임베디드 값 타입을 이용해 Member Entity를 정의해도 같은 create 쿼리가 나가고 같은 MEMBER 테이블이 만들어진다. 

**JpaMain**

```java
try {
  Member member = new Member();
  member.setUsername("member1");
  member.setHomeAddress(new Address("Seoul", "Street", "123456"));
  member.setWorkPeriod(new Period(LocalDateTime.MIN, LocalDateTime.MAX));

  em.persist(member);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%207.png)

### Embedded Type 특징

- Embedded Type도 결국 Entity의 값이다.
- **Embedded Type을 사용하나, 하지 않으나 매핑하는 테이블의 형태는 같다.**
- 객체와 테이블을 아주 세밀하게 매핑하는 것이 가능하다.
- 보통 잘 설계한 ORM 어플리케이션은 매핑 테이블 수보다 클래스 수가 더 많다.

### Embedded Type과 연관관계

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%208.png)

Embedded 값 타입이 Entity를 가질 수 있다. FK를 가진다.

### @AttributeOverrides : 속성 재정의

Member에 직장 주소도 필요해서 workAddress도 추가했다고 가정해보자.

```java
@Entity
public class Member {
  @Id
  @GeneratedValue
  @Column(name = "MEMBER_ID")
  private Long id;
  @Column(name = "USERNAME")
  private String username;

  // Period
  @Embedded
  private Period workPeriod;

  // Address
  @Embedded
  private Address homeAddress;
  @Embedded
  private Address workAddress;

	/* getter, setter */ 
}
```

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%209.png)

중복된 column이 들어갔다고 mapping exception이 발생한다.

**@AttributeOverrides, @AttributeOverride** 어노테이션으로 column명을 재정의하여 해결할 수 있다.

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

  // Period
  @Embedded
  private Period workPeriod;

  // Address
  @Embedded
  private Address homeAddress;
  @Embedded
  @AttributeOverrides({
      @AttributeOverride(name="city", column = @Column(name ="WORK_CITY")),
      @AttributeOverride(name="street", column = @Column(name ="WORK_STREET")),
      @AttributeOverride(name="zipcode", column = @Column(name ="WORK_ZIPCODE")),
  })
  private Address workAddress;

  /* getter, setter */ 
}
```

**JpaMain**

```java
try {
  Member member = new Member();
  member.setUsername("member1");
  member.setHomeAddress(new Address("Seoul", "Street", "123456"));
  member.setWorkAddress(new Address("Seongnam", "Migeum", "987654"));
  member.setWorkPeriod(new Period(LocalDateTime.MIN, LocalDateTime.MAX));

  em.persist(member);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}재ㅈ
```

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2010.png)

중복된 column명이 재정의되어 중복 문제를 해결했다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2011.png)

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2012.png)

### 값 타입 공유 문제

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2013.png)

하나의 값 타입을 여러 Entity가 공유할 경우 side effect가 반드시 발생한다. 

회원2의 주소만 변경했는데 회원1의 주소도 같이 변경된다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2014.png)

올바르게 바뀌려면 하나의 값 타입을 공유하는게 아니라 복사해서 각자 가지고 있어야 한다.

**JpaMain**

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  Address address = new Address("Seoul", "Street", "123456");
  member1.setHomeAddress(address);
  member1.setWorkPeriod(new Period(LocalDateTime.MIN, LocalDateTime.MAX));

  em.persist(member1);

  Member member2 = new Member();
  member2.setUsername("member2");
  member2.setHomeAddress(address);  //  member1과 같은 address 공유

  em.persist(member2);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

member1, member2가 같은 address를 공유하면

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2015.png)

DB에 member1, member2 모두 같은 값이 잘 들어간다.

**JpaMain**

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  Address address = new Address("Seoul", "Street", "123456");
  member1.setHomeAddress(address);
  member1.setWorkPeriod(new Period(LocalDateTime.MIN, LocalDateTime.MAX));

  em.persist(member1);

  Member member2 = new Member();
  member2.setUsername("member2");
  member2.setHomeAddress(address);  //  member1과 같은 address 공유

  em.persist(member2);

  member1.getHomeAddress().setCity("Seongnam"); //  member1의 주소 변경

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

문제는 위와 같이 member1의 값 타입 값을 변경하면

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2016.png)

분명 member1만 update했는데 update 쿼리가 2번 나가고

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2017.png)

member2의 주소도 변경되었다.

프로그램이 복잡할 수록 이런 버그는 정말 잡기 어렵다.

만약 공유해서 동시에 변경되는걸 의도했다면 값 타입이 아니라 Entity를 써야 한다.

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2014.png)

올바른 작동 방식은 member2가 member1의 address를 복사하여 별도의 address2를 만든 후 이를 값 타입으로 사용해야 한다.

**JpaMain**

```java
try {
  Member member1 = new Member();
  member1.setUsername("member1");
  Address address = new Address("Seoul", "Street", "123456");
  member1.setHomeAddress(address);
  member1.setWorkPeriod(new Period(LocalDateTime.MIN, LocalDateTime.MAX));

  em.persist(member1);

  Member member2 = new Member();
  member2.setUsername("member2");
  Address address2 = new Address(address.getCity(), address.getStreet(), address.getZipcode());
  member2.setHomeAddress(address2);  //  member1과 같은 address 공유

  em.persist(member2);

  member1.getHomeAddress().setCity("Seongnam"); //  member1의 주소 변경

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2018.png)

update 쿼리도 1번 나가고

![Untitled](24_02_09_daily_certification%20c8341137af994010812fd69d2235a652/Untitled%2019.png)

정상적으로 member1의 주소만 변경된다.

하지만 위의 잘못된 코드를 막을 방법이 없다.

**임베디드 타입은 객체 타입이므로 참조 값을 직접 대입하는 것을 compile 단계에서 막지 못한다.**

**객체의 공유 참조는 피할 수 없다. = 으로 대입했을 때 막을 방법이 없다.**

**값 타입을 불변 객체(Immutable Object)로 설계하면 위와 같은 부작용을 원천 차단할 수 있다.**

불변 객체는 생성자로만 값을 생성하고 setter를 정의하지 않거나 접근 제어자를 private로 바꾼다.

Integer, String이 대표적인 불변 객체다.

setter를 제거하면 값을 변경하려는 모든 시도는 compiler에 의해 차단된다.

그럼에도 값을 변경하고 싶다면 값 타입 객체 자체를 새로 만들어서 기존 값 타입 값을 복사하여 갈아끼워야 한다.

```java
try {
  Member member = new Member();
  member.setUsername("member");
  Address address = new Address("Seoul", "Street", "123456");
  member.setHomeAddress(address);
  member.setWorkPeriod(new Period(LocalDateTime.MIN, LocalDateTime.MAX));

  em.persist(member);

  //  불변 객체 address 값 바꾸려면 Address 객체를 새로 만들어서 갈아끼워야 함
  Address newAddress = new Address("Seongnam", "Street", "987654");
  member.setHomeAddress(newAddress);

  tx.commit();
} catch (Exception e) {
  tx.rollback();
  e.printStackTrace();
} finally {
  em.close();
}
```

### 값 타입의 비교

값 타입은 인스턴스 자체가 달라도(참조값이 달라도), 그 값이 같다면 같다고 판단되어야 한다.

- 동일성(identity) 비교 : 인스턴스의 참조 값 비교, == 사용
- 동등성(equivalence) 비교 : 인스턴스 값 비교, equals() 사용
- **값 타입은 a.equals(b)를 사용해서 동등성 비교를 해야 함**
- 값 타입의 equals() 메서드를 적절히 재정의해야 한다. 주로 모든 필드 값을 비교해서 같으면 같다 정의한다.

equals()를 재정의할 때는 가급적 IDE가 자동으로 작성해주는 재정의를 사용하자.

# Problem Solving (Algorithm & SQL)

**BOJ 6581 HTML**

[6581번: HTML](https://www.acmicpc.net/problem/6581)

입력 HTML을 한 줄씩 읽고, tokenizer로 띄어쓰기 단위로 단어를 파싱한다.

단어들을 처리하기 위한 buffer를 따로 준비하고 buffer 크기로 단어를 추가할지 관리한다.

1. 단어가 <br> 태그일 경우
    
    buffer 내용을 출력하고 buffer를 비운 후 줄바꿈을 한다.
    
2. 단어가 <hr> 태그일 경우
    
    HTML 문장 내에서 이전에 처리한 단어가 존재할 경우 buffer 내용을 출력 후 buffer를 비우고 줄바꿈을 한다. 그 다음에 수평선을 출력하고 줄바꿈을 한다.
    
3. 일반 단어일 경우
    1. 현재 buffer가 비었을 경우 그냥 추가하면 된다.
    2. 현재 buffer가 비어있지 않을 경우
        1. buffer 크기 + 띄어쓰기 + 단어 크기가 80을 넘을 경우 buffer를 출력하고 줄바꿈후 buffer를 비운 다음에 현재 단어를 buffer에 다시 추가한다.
        2. buffer 크기 + 띄어쓰기 + 단어 크기가 80 이하일 경우 띄어쓰기 추가 후 단어를 추가한다.

어렵진 않지만 문제 조건을 꼼꼼히 분석하고 구현해야 하는 좋은 문자열 파싱 연습 문제였다.

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_6581 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private val buffer = StringBuilder()

    fun solve() {
        while(true) {
            val input = br.readLine()   //  한 줄 단위로 입력받기

            if(input == null) { //  끝났을 경우
                if(buffer.isNotEmpty()) {   //  buffer가 비어있지 않을 경우
                    sb.append(buffer)       //  buffer 옮기기
                    buffer.clear()          //  buffer 비우기
                    sb.append("\n")         //  줄 바꾸기
                }

                print(sb)   //  파싱 결과 출력
                break
            }

            tokens = StringTokenizer(input) //  단어 단위로 파싱

            var before = "" //  해당 line에서 이전에 처리한 단어
            while(tokens.hasMoreTokens()) {
                val word = tokens.nextToken()

                if(word == "<br>") {    //  새 줄 시작하기
                    sb.append(buffer)   //  buffer에 존재하는 내용 옮기기
                    buffer.clear()      //  buffer 비우기
                    sb.append("\n")     //  줄 바꾸기
                }
                else if(word == "<hr>") {   //  수평선 긋기
                    if(before != "") {  //  이전에 처리한 단어가 있을 경우, 즉 <hr>이 가장 먼저 나온게 아닐 경우
                        if(buffer.isNotEmpty()) {   //  buffer에 내용 남았을 경우
                            sb.append(buffer)   //  buffer에 존재하는 내용 옮기기
                            buffer.clear()      //  buffer 비우기
                            sb.append("\n")     //  줄 바꾸기
                        }

                        repeat(80) {
                            sb.append("-")
                        }
                        sb.append("\n")
                    }
                }
                else {  //  일반 단어일 경우
                    if(buffer.isEmpty())    //  buffer가 비었을 경우
                        buffer.append(word) //  띄어쓰기 없이 단어 추가
                    else {
                        val curSize = buffer.length //  현재 buffer 크기

                        if(curSize + 1 + word.length > 80) { //  현재 buffer + 띄어쓰기 + 현재 단어 길이 > 80일 경우
                            sb.append(buffer)   //  buffer에 있는 내용 옮기기
                            buffer.clear()      //  buffer 비우기
                            sb.append("\n")     //  줄 바꾸기
                            buffer.append(word) //  새로운 buffer에 단어 추가하기
                        }
                        else // 띄어쓰고 현재 단어 추가해도 길이가 80을 넘지 않을 경우
                            buffer.append(" $word")
                    }
                }

                before = word
            }
        }
    }
}

fun main() {
    BOJ_6581().solve()
}
```