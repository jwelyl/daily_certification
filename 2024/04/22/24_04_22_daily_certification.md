# 24_04_22_daily_certification

# 데이터베이스

# Normalization

## **데이터베이스 Schema를 잘못 설계했을 때 발생할 수 있는 문제**

### 중복 데이터 문제

다음과 같이 임직원 정보와 부서 정보를 함께 저장하는 테이블을 고려해보자.

**EMPLOYEE_DEPARTMENT 테이블**

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |

위와 같은 테이블에 데이터를 write(insert, update, delete)할 경우 발생할 수 있는 문제들(Anomalies)에 대해 알아보자.

## Anomalies

### **Insertion Anomalies**

**EMPLOYEE_DEPARTMENT 테이블**

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |

위 테이블에 개발팀(DEV) 소속의 사원 MESSI, JINHO의 정보를 입력하자.

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |

MESSI, JINHO 모두 개발팀(DEV) 소속이다. 

**dept_id, dept_name, dept_leader_id에 중복된 데이터가 들어가게 된다.**

**중복된 데이터의 문제점**

- 저장 공간을 낭비하게 된다.
- **실수로 인한 데이터의 불일치 가능성이 존재하게 된다.**
    
    
    | empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
    | --- | --- | --- | --- | --- | --- | --- | --- |
    | 1 | MESSI | … | … | … | 1001 | DEV | 1 |
    | 2 | JINHO | … | … | … | 1001 | DEB | 1 |
    
    위와 같이 같은 개발팀 데이터임에도 dept_name에 불일치가 발생할 수 있다.
    

이번에는 테이블에 아직 부서가 정해지지 않은 JENNY라는 사원의 정보를 넣어보자.

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |

JENNY의 dept_id, dept_name, dept_leader_id에 **NULL이 들어가게 된다.**

**NULL 값은 최대한 적게 들어가는 것이 좋다.**

이번에는 테이블에 임직원이 한 명도 없는 신생 부서 QA의 정보를 넣어보자.

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |
| 4 | NULL | NULL | NULL | NULL | 1002 | QA | NULL |

이 테이블은 임직원 정보도 한 번에 표현해야 하므로 empl_id, empl_name, birth_date, position, salary에는 NULL을 넣어줘야 한다. 하지만 **empl_id의 경우 PK이므로 NULL이 될 수 없다.**

QA 부서 저장 용 tuple을 만들기 위해 NULL도 많이 사용해야 하고 PK를 위해 4라는 값을 사용해야 한다.

만약 위 상태에서 QA 부서 사원 YUJIN이 들어온다면?

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |
| 4 | NULL | NULL | NULL | NULL | 1002 | QA | NULL |
| 5 | YUJIN | … | … | … | 1002 | QA | 5 |

QA 부서 표현을 위해 존재했던 4번째 tuple은 삭제해야 한다. 매끄럽지도 않고 매우 번거롭다.

### 위와 같은 문제가 생기는 이유

**별개의 관심사인 EMPLOYEE와 DEPARTMENT가 하나의 테이블에 존재하기 때문이다.**

각각에 대해 따로 테이블이 존재해야 한다.

### **해결 (테이블 분리)**

위의 **EMPLOYEE_DEPARTMENT 테이블을 EMPLOYEE와 DEPARTMENT 테이블로 나누면 다음과 같다.**

**EMPLOYEE**

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 |
| 2 | JINHO | … | … | … | 1001 |
| 3 | JENNY | … | … | … | NULL |
| 5 | YUJIN | … | … | … | 1002 |

**DEPARTMENT**

| dept_id | dept_name | dept_leader_id |
| --- | --- | --- |
| 1001 | DEV | 1 |
| 1002 | QA | 5 |

만약 SIMON이라는 사원을 추가하려면 EMPLOYEE 테이블에만 추가해주면 된다.

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 |
| 2 | JINHO | … | … | … | 1001 |
| 3 | JENNY | … | … | … | NULL |
| 5 | YUJIN | … | … | … | 1002 |
| 6 | SIMON | … | … | … | ? |

SIMON이 부서를 배정받지 못했다면 dept_id는 NULL로, 부서를 배정받았다면 DEPARTMENT 테이블에 존재하는 부서의 dept_id로 넣어주면 된다. 

**사원을 추가하는데 DEPARTMENT 테이블을 수정할 필요가 없다.**

만약 아직 소속 사원이 없는 새로운 부서 DESIGN을 추가하려면 DEPARTMENT 테이블에만 추가해주면 된다.

| dept_id | dept_name | dept_leader_id |
| --- | --- | --- |
| 1001 | DEV | 1 |
| 1002 | QA | 5 |
| 1003 | DESIGN | NULL |

아직 소속 사원이 없으므로 dept_leader도 존재하지 않으므로 dept_leader_id만 NULL로 해주면 된다. 

**부서를 추가하는데 EMPLOYEE 테이블을 수정할 필요가 없다.**

### **Deletion Anomalies**

**EMPLOYEE_DEPARTMENT 테이블**

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |
| 5 | YUJIN | … | … | … | 1002 | QA | 5 |

위 테이블에서 YUJIN 정보를 삭제하면 어떻게 될까?

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |

YUJIN이라는 사원 정보만 삭제하기를 의도했는데, QA라는 부서 정보까지 함께 삭제해버렸다.

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |
| 5 | NULL | NULL | NULL | NULL | 1002 | QA | 5 |

QA 부서 정보를 유지하기 위해 사원 정보 부분만 NULL 처리할 수도 있긴하다.

하지만 PK인 empl_id는 5로 유지되어야 하며, NULL 값이 너무 많아지는 문제가 있다.

### **해결 (테이블 분리)**

위의 **EMPLOYEE_DEPARTMENT 테이블을 EMPLOYEE와 DEPARTMENT 테이블로 나누면 다음과 같다.**

**EMPLOYEE**

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 |
| 2 | JINHO | … | … | … | 1001 |
| 3 | JENNY | … | … | … | NULL |
| 5 | YUJIN | … | … | … | 1002 |

**DEPARTMENT**

| dept_id | dept_name | dept_leader_id |
| --- | --- | --- |
| 1001 | DEV | 1 |
| 1002 | QA | 5 |

만약 YUJIN이라는 사원을 삭제하려면 EMPLOYEE 테이블에서만 삭제해주면 된다.

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 |
| 2 | JINHO | … | … | … | 1001 |
| 3 | JENNY | … | … | … | NULL |

**사원을 제거하는데 DEPARTMENT 테이블을 수정할 필요가 없다.**

### **Update Anomalies**

**EMPLOYEE_DEPARTMENT 테이블**

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |

개발팀의 부서 이름이 DEV에서 DEV1으로 변경되었다고 가정하자.

| empl_id | empl_name | birth_date | position | salary | dept_id | dept_name | dept_leader_id |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 | DEV | 1 |
| 2 | JINHO | … | … | … | 1001 | DEV1 | 1 |
| 3 | JENNY | … | … | … | NULL | NULL | NULL |

MESSI와 JINHO 튜플 모두 dept_name의 DEV를 DEV1으로 변경해야 하지만 무슨 이유에서인지 JINHO에서만 DEV1으로 업데이트되어 부서 정보의 불일치가 발생했다.

### **해결 (테이블 분리)**

위의 **EMPLOYEE_DEPARTMENT 테이블을 EMPLOYEE와 DEPARTMENT 테이블로 나누면 다음과 같다.**

**EMPLOYEE**

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |
| 1 | MESSI | … | … | … | 1001 |
| 2 | JINHO | … | … | … | 1001 |
| 3 | JENNY | … | … | … | NULL |

**DEPARTMENT**

| dept_id | dept_name | dept_leader_id |
| --- | --- | --- |
| 1001 | DEV | 1 |
| 1002 | QA | 5 |

개발팀의 부서 이름을 DEV에서 DEV1으로 변경하려면 DEPARTMENT 테이블에서만 변경해주면 된다.

| dept_id | dept_name | dept_leader_id |
| --- | --- | --- |
| 1001 | DEV1 | 1 |
| 1002 | QA | 5 |

**부서 정보를 수정하는데 EMPLOYEE 테이블을 수정할 필요가 없으며 데이터 불일치가 발생하지도 않는다.**

### Spurious Tuples (가짜 튜플)

다음과 같은 데이터베이스 테이블 설계를 생각해보자.

**DEPARTMENT_PROJECT**

| dept_id | proj_id | proj_name | proj_location |
| --- | --- | --- | --- |
| 1001 | 2001 | Beautiful Jeju | Jeju |
| 1001 | 2003 | Busan Romance | Haeundae |
| 1002 | 2002 | Wedding At Jeju | Jeju |
| 1003 | 2004 | Again 2002 | Gwanghwamun |

**DEPARTMENT_LOCATION**

| dept_name | proj_location |
| --- | --- |
| feelm | Jeju |
| feelm | Haeundae |
| Picachoo | Jeju |
| Moviemove | Gwanghwamun |

위의 두 테이블을 Natural Join해보면 다음과 같다. 두 테이블이 공통적으로 proj_location attribute를 가지므로 proj_location으로 join된다.

```sql
select * from DEPARTMENT_PROJECT natural join DEPARTMENT_LOCATION;
```

결과

| dept_id | proj_id | proj_name | proj_location | dept_name |
| --- | --- | --- | --- | --- |
| 1001 | 2001 | Beautiful Jeju | Jeju | feelm |
| 1001 | 2003 | Busan Romance | Haeundae | feelm |
| 1002 | 2002 | Wedding At Jeju | Jeju | Picachoo |
| 1003 | 2004 | Again 2002 | Gwanghwamun | Moviemove |
| 1001 | 2001 | Beautiful Jeju | Jeju | Picachoo |
| 1002 | 2002 | Wedding At Jeju | Jeju | feelm |

위와 같은 결과가 나온다. 우리가 예상한 결과는 아래와 같은데 다른 결과가 나온 것이다.

| dept_id | proj_id | proj_name | proj_location | dept_name |
| --- | --- | --- | --- | --- |
| 1001 | 2001 | Beautiful Jeju | Jeju | feelm |
| 1001 | 2003 | Busan Romance | Haeundae | feelm |
| 1002 | 2002 | Wedding At Jeju | Jeju | Picachoo |
| 1003 | 2004 | Again 2002 | Gwanghwamun | Moviemove |

proj_location 기준 DEPARTMENT_PROJECT의 첫 번째 튜플의 Jeju와 매칭되는 DEPARTMENT_LOCATION의 튜플 개수는 2개이고 세 번째 튜플의 Jeju와 매칭되는 튜플 개수도 2개이다.

| 1001 | 2001 | Beautiful Jeju | Jeju | Picachoo |
| --- | --- | --- | --- | --- |
| 1002 | 2002 | Wedding At Jeju | Jeju | feelm |

JOIN하면서 생긴 위의 두 튜플은 가짜 정보이다. 이런 튜플을 Spurious Tuple이라고 한다.

### **해결 (테이블 분리)**

**DEPARTMENT**

| dept_id | dept_name |
| --- | --- |

**PROJECT**

| proj_id | proj_name | proj_location |
| --- | --- | --- |

**DEPARTMENT_PROJECT**

| dept_id | proj_id |
| --- | --- |

### NULL

**NULL 값이 많아짐으로 인한 문제점들**

- NULL 값이 있는 column으로 join하는 경우 상황에 따라 예상과 다른 결과가 발생한다.
- NULL 값이 있는 column에 aggregate function을 사용했을 때 주의가 필요하다.
    - ex) count, count(*)
- 불필요한 storage를 낭비한다.

### 올바른 Database Schema 설계

1. 의미적으로 관련 있는 속성들끼리 테이블 구성
2. 중복 데이터를 최대한 허용하지 않도록 설계
3. JOIN 수행 시 가짜 데이터가 생기지 않도록 설계
4. 되도록 NULL 값을 줄일 수 있는 방향으로 설계 

## Functional Dependency 정의

### Functional Dependency

**한 테이블에 있는 두 개의 Attribute(s) 집합(Set) 사이의 제약(A constraint)**

예시

**EMPLOYEE**

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |

EMPLOYEE 테이블의 attributes들을 두 개의 집합 X, Y로 나누어보자.

**X = {empl_id}**

**Y = {empl_name, birth_date, position, salary}**

이 때 EMPLOYEE 테이블의 서로 다른 두 튜플에 대해 X의 값이 같으면 Y의 값도 같다.

이렇게 X의 값에 따라 Y의 값이 유일하게(Uniquely) 결정될 때,

**X가 Y를 함수적으로 결정한다(Functionally Determine)**

**Y가 X에 함수적으로 의존한다(Functionally Dependent)**

라고 한다. 

기호로는 **X → Y** 로 나타낸다.

**이 때 두 집합 X, Y 사이의 제약 관계를 Functional Dependency(FD)라고 한다.**

### Functional Dependency 파악

FD를 파악할 때는 테이블의 schema를 보고 **의미적으로 파악해야 한다.**

**테이블의 특정 state만 보고 FD를 파악해서는 안된다.**

예시

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |
| … | Messi | 1986-01-13 | … | … | … |
| … | Sony | 1993-09-17 | … | … | … |
| … | Jinho | 1982-02-02 | … | … | … |
| … | Joy | 2007-07-07 | … | … | … |

위의 테이블 상태만 보면 {empl_name} → {birth_date}인 것 같다. 하지만,

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |
| … | Messi | 1986-01-13 | … | … | … |
| … | Sony | 1993-09-17 | … | … | … |
| … | Jinho | 1982-02-02 | … | … | … |
| … | Joy | 2007-07-07 | … | … | … |
| … | Jinho | 1992-04-24 | … | … | … |

Jinho라는 사원이 한 명 더 추가되면 더 이상 {empl_name} → {birth_date}가 성립하지 않는다.

**EMPLOYEE**

| empl_id | empl_name | birth_date | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- |

EMPLOYEE 테이블의 attributes들을 두 개의 집합 X, Y로 나누어보자.

**X = {empl_id}**

**Y = {empl_name, birth_date, position, salary, dept_id}**

일 경우 X → Y 는 성립할까?

만약 모든 임직원이 하나의 부서에만 속해야 하는 경우 X → Y 가 성립한다.

하지만 어떤 임직원은 2개 이상의 부서에 속할 수 있을 경우 X → Y 가 성립하지 않는다.

### Functional Dependency 예시

![Untitled](24_04_22_daily_certification%20b338dd202f83468da5167a7c1a69df87/Untitled.png)

**X → Y 라고 해서 항상 Y → X 를 의미하지 않는다.**

ex) {empl_id} → {empl_name} 은 성립하지만 {empl_name} → {empl_id}  은 성립하지 않는다.

ex) {empl_id} → {SSN}, {SSN} → {empl_id}  은 모두 성립한다. SSN은 주민등록번호

**{} → Y 는 Y가 언제나 하나의 값을 가질 때 성립한다.**

**PROJECT**

| proj_id | proj_name | start_date | end_date | leader_id | company |
| --- | --- | --- | --- | --- | --- |
| 1001 | … | … | … | … | ez. |
| 1002 | … | … | … | … | ez. |
| 1003 | … | … | … | … | ez. |

company 값이 항상 ez.라면 {} → {company} 가 성립한다.

정규화 중 제 2 정규화(2NF)와 관련 있다.

## Functional Dependency 종류

### Trivial Functional Dependency

![Untitled](24_04_22_daily_certification%20b338dd202f83468da5167a7c1a69df87/Untitled%201.png)

**Y ⊆ X 인 경우, X → Y는 항상 성립한다. 이러한 FD를 Trivial FD라고 한다.**

### Non-trivial Functional Dependency

![Untitled](24_04_22_daily_certification%20b338dd202f83468da5167a7c1a69df87/Untitled%202.png)

**X → Y 이고 Y가 X의 부분집합이 아닌 경우, 이러한 FD를 Non-trivial FD라고 한다.**

특히 {a, b, c} → {d, e} 에서와 같이 **X와 Y의 교집합이 {}일 경우를 Completely Non-trivial FD라고 한다.**

### Partial Functional Dependency

![Untitled](24_04_22_daily_certification%20b338dd202f83468da5167a7c1a69df87/Untitled%203.png)

**X → Y 이고 어떤 X’ ⊂ X일 때, X’ → Y 인 경우 X → Y 는 Partial FD 이다.**

예를 들어 {empl_id, empl_name} → {birth_date} 가 성립하고, {empl_id} → {birth_date}도 성립할 경우,

 {empl_id, empl_name} → {birth_date}는 Partial FD이다.

### Full Functional Dependency

![Untitled](24_04_22_daily_certification%20b338dd202f83468da5167a7c1a69df87/Untitled%204.png)

**X → Y 이고 모든 X’ ⊂ X일 때, X’ → Y 가 성립하지 않는 경우,  X → Y 는  Full FD 이다.**

예를 들어 {stud_id, class_id} → {grade} 가 성립하고, {stud_id} → {grade},  {class_id} → {grade}, {} → {grade} 모두 성립하지 않을 경우,  {stud_id, class_id} → {grade}는 Full FD이다.

**Full FD는 정규화에서 매우 중요하다.**

# Problem Solving (Algorithm & SQL)

**CO{)E TREE 범위 내에 있는 점의 수 2**

[https://www.codetree.ai/missions/8/problems/the-number-of-points-within-the-range-2/description](https://www.codetree.ai/missions/8/problems/the-number-of-points-within-the-range-2/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-number-of-points-within-the-range-2/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final int MAX = 1_000_002;

    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  점의 개수
    private static int q;   //  범위 개수

    private static final boolean[] pos = new boolean[MAX];  //  pos[i] : 위치에 점이 있을 경우 true
    private static final int[] prefixCnt = new int[MAX];  //  prefixCnt[i] : 위치 1부터 i까지 누적 점의 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        q = Integer.parseInt(tokens.nextToken());

        tokens = new StringTokenizer(br.readLine());
        for(int i = 1; i <= n; i++)
            pos[Integer.parseInt(tokens.nextToken()) + 1] = true;

        for(int i = 1; i < MAX; i++)
            prefixCnt[i] = prefixCnt[i - 1] + (pos[i] ? 1 : 0);

        for(int i = 0; i < q; i++) {
            tokens = new StringTokenizer(br.readLine());

            int from = Integer.parseInt(tokens.nextToken()) + 1;
            int to = Integer.parseInt(tokens.nextToken()) + 1;

            sb.append(prefixCnt[to] - prefixCnt[from - 1]).append("\n");
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```

**CO{)E TREE 구간에 속한 문자의 개수**

[https://www.codetree.ai/missions/8/problems/the-number-of-characters-in-the-interval/description](https://www.codetree.ai/missions/8/problems/the-number-of-characters-in-the-interval/description)

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/8/problems/the-number-of-characters-in-the-interval/description)

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer tokens;

    private static int n;   //  세로 길이
    private static int m;   //  가로 길이
    private static int k;   //  질의 개수

    private static int[][] aCnt; // aCnt[i][j] : (1, 1)부터 (i, j)까지 a의 개수
    private static int[][] bCnt; // bCnt[i][j] : (1, 1)부터 (i, j)까지 b의 개수
    private static int[][] cCnt; // cCnt[i][j] : (1, 1)부터 (i, j)까지 c의 개수

    public static void main(String[] args) throws IOException {
        tokens = new StringTokenizer(br.readLine());
        n = Integer.parseInt(tokens.nextToken());
        m = Integer.parseInt(tokens.nextToken());
        k = Integer.parseInt(tokens.nextToken());

        aCnt = new int[n + 1][m + 1];
        bCnt = new int[n + 1][m + 1];
        cCnt = new int[n + 1][m + 1];

        for(int i = 0; i < n; i++) {
            String input = br.readLine();

            for(int j = 0; j < m; j++) {
                char ch = input.charAt(j);

                switch(ch) {
                case 'a':
                    aCnt[i + 1][j + 1] = aCnt[i + 1][j] + aCnt[i][j + 1] - aCnt[i][j] + 1;
                    bCnt[i + 1][j + 1] = bCnt[i + 1][j] + bCnt[i][j + 1] - bCnt[i][j];
                    cCnt[i + 1][j + 1] = cCnt[i + 1][j] + cCnt[i][j + 1] - cCnt[i][j];
                    break;
                case 'b':
                    aCnt[i + 1][j + 1] = aCnt[i + 1][j] + aCnt[i][j + 1] - aCnt[i][j];
                    bCnt[i + 1][j + 1] = bCnt[i + 1][j] + bCnt[i][j + 1] - bCnt[i][j] + 1;
                    cCnt[i + 1][j + 1] = cCnt[i + 1][j] + cCnt[i][j + 1] - cCnt[i][j];
                    break;
                case 'c':
                    aCnt[i + 1][j + 1] = aCnt[i + 1][j] + aCnt[i][j + 1] - aCnt[i][j];
                    bCnt[i + 1][j + 1] = bCnt[i + 1][j] + bCnt[i][j + 1] - bCnt[i][j];
                    cCnt[i + 1][j + 1] = cCnt[i + 1][j] + cCnt[i][j + 1] - cCnt[i][j] + 1;
                    break;
                }
            }
        }

        for(int i = 0; i < k; i++) {
            tokens = new StringTokenizer(br.readLine());

            int sy = Integer.parseInt(tokens.nextToken());
            int sx = Integer.parseInt(tokens.nextToken());
            int ey = Integer.parseInt(tokens.nextToken());
            int ex = Integer.parseInt(tokens.nextToken());

            sb.append(aCnt[ey][ex] - aCnt[sy - 1][ex] - aCnt[ey][sx - 1] + aCnt[sy - 1][sx - 1]).append(" ");
            sb.append(bCnt[ey][ex] - bCnt[sy - 1][ex] - bCnt[ey][sx - 1] + bCnt[sy - 1][sx - 1]).append(" ");
            sb.append(cCnt[ey][ex] - cCnt[sy - 1][ex] - cCnt[ey][sx - 1] + cCnt[sy - 1][sx - 1]).append("\n");
        }

        System.out.print(sb);
    }   //  main-end
}   //  Main-class-end
```