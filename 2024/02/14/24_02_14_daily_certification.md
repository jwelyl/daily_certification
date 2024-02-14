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

# Typescript

## Call Signature

**Traditional Function**

```tsx
//  call signature
//  function add1: (a: number, b: number): number
function add1(a: number, b: number): number {
  return a + b;
}
```

**Arrow Function**

```tsx
//  call signature
//  const add2: (a: number, b: number) => number
const add2 = (a: number, b: number): number => a + b;
```

**Call Signature**

함수의 선언부, 함수의 이름과 어떤 parameter를 받고, 어떤 값을 반환하는지를 알 수 있다.

즉 함수의 호출 방법을 알수 있다.

**Call Signature 정의**

두 개의 number를 parameter로 받고, number를 반환하는 함수의 Call Signature를 정의하자.

```tsx
//  declaring call signature
type Add = (a: number, b: number) => number;
```

또는

```tsx
//  declaring call signature
type Add = {
  (a: number, b: number): number;
};
```

정의한 Add call signature로 함수를 정의할 수 있다.

```tsx
//  parameter type 생략 가능
const add3: Add = (a, b) => a + b;
```

Call Signature를 이용하면 parameter의 type을 생략할 수 있다.

또한 React에서 Props로 함수를 보낼 때, 함수의 사용법을 알려줄 수 있다.

## Overloading

**call signature를 이용한 overloading**

**parameter 개수가 같을 경우**

```tsx
//  declaring call signature
//  overloading
type Add = {
  (a: number, b: number): number;
  (a: number, b: string): number;
};
```

call signature type에 오버로딩할 함수의 call signature를 모아두는 것으로 오버로딩을 할 수 있다.

만약 위와 같이 오버로딩할 경우 다음 함수 정의는 문제가 된다.

```tsx
const add: Add = (a, b) => a + b;
```

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%208.png)

b가 number일 수도, string일 수도 있기 때문에 +를 할 수 없다고 한다.

```tsx
const add: Add = (a, b) => {
  if (typeof b === "string") return a;
  return a + b;
};
```

이런 식으로 사용하면 사용할 수는 있다.

**다른 예시 (next.js의 Router)**

```tsx
type Config = {
  path: string;
  state: object;
};

type Push = {
  (path: string): void;
  (config: Config): void;
};

const push: Push = (config) => {
  if (typeof config === "string") console.log(config);
  else {
    console.log(config.path);
  }
};
```

**parameter 개수가 다를 경우**

```tsx
//  declaring call signature
//  overloading
type Add = {
  (a: number, b: number): number;
  (a: number, b: number, c: number): number; // c는 optional함
};
```

a, b를 parameter로 가지는 메서드와 a, b, c를 parameter로 가지는 메서드를 오버로딩할 경우

```tsx
const add: Add = (a, b) => {
  return a + b;
};
```

parameter가 2개인 함수는 정의가 잘 되지만, 3개인 함수를 정의하면

```tsx
const add: Add = (a, b, c) => {
  return a + b;
};
```

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%209.png)

위와 같이 오류가 발생한다. c는 optional하기 때문이다.

따라서 c까지 parameter로 받으려면 다음과 같이 정의해야 한다. c가 optional이란 것을 명시해야 한다.

```tsx
const add: Add = (a, b, c?: number) => {
  if (c) return a + b + c;
  return a + b;
};
```

**call signature로 오버로딩을 하는 것이다. add라는 화살표 함수는 여전히 하나만 존재할 수 있다.**

이런 오버로딩은 불가하다.

```tsx
const add = (num1: number, num2: number) => num1 + num2;
const add = (num1: number, num2: number, num3: number) => num1 + num2 + num3;
```

**Java와 overloading 방식이 다름을 기억하자.**

**사실 Javascript에서는 정식으로 overloading을 지원하지 않는다.**

## Generic Type & Polymorphism

```tsx
type SuperPrint = {
  (arr: number[]): void;
  (arr: boolean[]): void;
};

const superPrint: SuperPrint = (arr) => {
  arr.forEach((i) => console.log(i));
};

superPrint([1, 2, 3, 4]);
superPrint([true, false, true]);
superPrint(["apple", "tomato", "banana"]); // syntax error
```

마지막 호출은 string[]에 대해 오버로딩 되지 않았으므로 오류가 발생한다.

그렇다고 다음과 같이 SuperPrint를 고치는 것은 현명하지 않다. 당장 string[]은 출력이 되겠지만, 다른 타입을 출력하고 싶으면 계속 signature를 추가해야 한다.

```tsx
type SuperPrint = {
  (arr: number[]): void;
  (arr: boolean[]): void;
  (arr: string[]): void;
};
```

이런 것도 해결할 수 없다.

```tsx
superPrint([1, "2", true, false]);
```

모든 경우의 입력을 해결하려면 결국 Generic을 이용해야 한다.

number, boolean, string과 같은 type을 concrete type이라고 한다.

**Generic을 사용한 함수 오버로딩**

```tsx
type SuperPrint = {
  <TypePlaceholder>(arr: TypePlaceholder[]): void;
};

const superPrint: SuperPrint = (arr) => {
  arr.forEach((i) => console.log(i));
};

superPrint([1, 2, 3, 4]);
superPrint([true, false, true]);
superPrint(["apple", "tomato", "banana"]);
superPrint([1, "2", true, false]);
```

Generic을 통해 입력한 인자의 타입을 추론하여 알맞은 타입으로 맞춰서 작동한다.

```tsx
type SuperPrint = {
  <TypePlaceholder>(arr: TypePlaceholder[]): TypePlaceholder;
};

const superPrint: SuperPrint = (arr) => arr[0];

const a = superPrint([1, 2, 3, 4]);
const b = superPrint([true, false, true]);
const c = superPrint(["apple", "tomato", "banana"]);
const d = superPrint([1, "2", true, false]);

console.log(a, b, c, d);
```

반환형에도 Generic을 사용할 수 있다. 보통 Generic Type으로 다음 같이 T를 사용한다.

```tsx
type SuperPrint = {
  <T>(arr: T[]): T;
};

const superPrint: SuperPrint = (arr) => arr[0];

const a = superPrint([1, 2, 3, 4]);
const b = superPrint([true, false, true]);
const c = superPrint(["apple", "tomato", "banana"]);
const d = superPrint([1, "2", true, false]);
```

```tsx
function superPrint<V>(a: V[]): V {
  return a[0];
}

const a = superPrint([1, 2, 3, 4]);
const b = superPrint([true, false, true]);
const c = superPrint(["apple", "tomato", "banana"]);
const d = superPrint([1, "2", true, false]);
```

generic을 이용해 Type을 정의하는 것도 가능하다.

```tsx
type Player<E> = {
  name: string;
  extraInfo: E;
};

type KoreiiPlayer = Player<{ favFood: string }>;

const koreii1: Player<{ favFood: string }> = {
  name: "koreii1",
  extraInfo: {
    favFood: "sushi",
  },
};

const koreii2: KoreiiPlayer = {
  name: "koreii2",
  extraInfo: {
    favFood: "sushi",
  },
};

const jwelyl: Player<null> = {
  name: "jwelyl",
  extraInfo: null,
};
```

```tsx

```

# Problem Solving (Algorithm & SQL)

**BOJ 2109 순회강연**

[2109번: 순회강연](https://www.acmicpc.net/problem/2109)

이전에 풀었던 1781 컵라면 문제와 유사하다.

[1781번: 컵라면](https://www.acmicpc.net/problem/1781)

우선순위 큐에는 강연할 수 있는 강의들만 담겨 있다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%2010.png)

우선순위 큐의 크기가 곧 강연할 날의 수이다. 강연의 d보다 우선순위 큐의 크기가 크다면 해당 강연을 하기에는 이미 잡혀있는 강연이 너무 많은 것이다. 그럴 경우 해당 강연을 포함하여 기존에 잡혀있는 강연 중 가장 가치가 작은 강연부터 배제한다.

일단 강연 날짜가 촉박한 것부터 해결해 나가야 하므로 강연 날짜가 빠른 순으로 정렬한다.

![Untitled](24_02_14_daily_certification%2065efb5e707c6434dbe18097670175997/Untitled%2011.png)

**코드**

```kotlin
import java.util.*
import java.io.*

class BOJ_2109 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private lateinit var tokens : StringTokenizer

    private var n = 0    //    강연 수
    private val lectureList = ArrayList<Lecture>()    //    강연 기한 촉박한 순으로
    private val pq = PriorityQueue<Int>()    //    강연 보상 작은 순으로

    private var ans = 0    //    최대 보상

    fun solve() {
        n = br.readLine().toInt()

        repeat(n) {
            tokens = StringTokenizer(br.readLine())
            lectureList.add(Lecture(tokens.nextToken().toInt(), tokens.nextToken().toInt()))
        }

        lectureList.sort()    //    강연 기한이 빠른 순으로 정렬

        for(i in 0 ..< n) {
            val lecture = lectureList[i]
            pq.offer(lecture.p)

            if(lecture.d < pq.size)    //    해당 강연을 하기엔 이미 잡힌 강연이 많을 경우
                pq.poll()    //    가장 가치가 작은 강연 제거
        }

        while(pq.isNotEmpty())    //    남은 강연들은 모두 강연 가능함 (가치 큰 것들만)
            ans += pq.poll()

        println(ans)
    }

    private class Lecture(val p : Int, val d : Int) : Comparable<Lecture> {
        override fun compareTo(other : Lecture) : Int {
            return this.d.compareTo(other.d)
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
