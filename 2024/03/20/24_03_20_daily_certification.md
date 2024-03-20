# 24_03_20_daily_certification

```
[koreii] #80 데일리인증
데이터베이스
- Relational Data Model
- Relational Database의 정의, 특징
- NULL
- Key
- constraints
알고리즘 & 코딩 테스트 대비
- Relational Database Key 관련 문제 풀이
```

# Databse

## Relational Data Model

Relation은 수학의 Set에서 비롯된 개념

### Relation

student

### Domain (값들의 집합)

**더 이상 나누어 질 수 없는 Atomic한 값의 집합**

- student_ids
- student_names
- university_grades
- major_names
- phone_numbers
- phone_numbers (비상 연락망)

### Attribute

Domain이 Relation에서 맡은 역할 이름

- **id (**student_ids)
- **name** (student_names)
- **grade** (university_grades)
- **major** (major_names)
- **phone_num** (phone_numbers)
- **emer_phone_num** (phone_numbers)

### tuple

각 Attribute 값으로 이루어진 리스트. 일부 값은 NULL일 수 있다.

### Relation

tuple의 집합

### Domain of student Relation

![domain_relation.jpeg](24_03_20_daily_certification%205ebf04c29b2c49a2b22c232bcc034b1f/domain_relation.jpeg)

보통 tuple, relation은 테이블로 나타내는 것이 더 보기 좋다.

**student**

| id | name | grade | major | phone_num | emer_phone_num |
| --- | --- | --- | --- | --- | --- |
| 2023645 | cork | 3 | Front-End | 010-9231-6314 | 010-3237-4452 |
| 20161663 | koreii | 1 | Back-End | 010-9128-3459 | 010-7181-2434 |
| 20110217 | Jwelyl | 2 | Computer | 010-5712-2345 | 010-6414-4247 |

table에서 id, name, grade, major, phone_num, emer_phone_num이 attribute이고 3개의 row 각각이 tuple이다. 모든 tuple의 집합이 relation state이고 테이블 자체가 relation(table)이 된다. student는 relation name이 된다.

### Relation Schema

Relation의 구조를 나타낸다.

```sql
STUDENT(id, name, grade, major, phone_num, emer_phone_num)
```

relation 이름과 attributes 리스트로 표현되며 attributes와 관련된 constrains도 포함된다.

### Degree of Relation

Relation Schema의 Attributes 개수

## Relational Database (관계형 데이터베이스)

### Relational Database

- Relational Data Model에 기반하여 구조화된 Database
- 여러 개의 Relations로 구성된다.

### Relational Database Schema

Relation Schema Set + Integrity Constraints Set

## Relational Database (관계형 데이터베이스)의 특징

### Relation은 중복된 tuple을 가질 수 없다.

Relation 자체가 수학의 Set에서 비롯된 것이므로 중복된 원소를 가질 수 없다.

### Relation의 tuple을 식별하기 위해 attributes의 부분 집합을 key로 설정한다.

Key는 매우 중요한 개념이므로 아래에 따로 설명한다.

### Relation의 tuple의 순서는 중요하지 않다.

원하는 정렬 기준에 따라 ordering할 수 있다.

### 하나의 Relation에서 attributes의 이름은 중복되서는 안된다.

### Relation의 tuple에서 attributes의 순서는 중요하지 않다.

### Relation에서 attributes는  atomic해야 한다.

composite, multivalued attribute는 허용되지 않는다.

| id | name | address | grade | major |
| --- | --- | --- | --- | --- |
| 2023645 | cork | 경기도 성남시 분당구 구미동 | 3 | Front-End, Design |
| 20161663 | koreii | 서울특별시 강남구 역삼동 | 1 | Back-End |
| 20110217 | Jwelyl | 광주광역시 광산구 운수동 | 2 | Computer |

address의 경우 도, 시, 군/구, 동을 따로 나눌 수 있으므로 atomic하지 않고 composite하다.

major의 경우 값이 2개가 있으므로 multivalued이다.

## NULL

**NULL은 다음과 같은 의미로 해석될 수 있다.**

- 값이 존재하지 않는다.
- 값이 존재하나 그 값이 무엇인지 알지 못한다.
- 해당 사항과 관련이 없다.

| id | name | grade | major | toeic_score | transfer_from |
| --- | --- | --- | --- | --- | --- |
| 2023645 | cork | 3 | Front-End | NULL | 서강대학교 |
| 20161663 | koreii | 1 | Back-End | 987 | NULL |
| 20110217 | Jwelyl | 2 | Computer | 321 | NULL |

**NULL은 그 존재만으로 의미가 명확하지 않다.** cork의 toeic_score가 NULL이면 해석하기에 따라 cork가 토익을 응시한 적이 없을 수도 있고, 응시했지만 아직 결과가 나오지 않아서 NULL일 수도 있고, 최악의 경우 응시했고 결과도 나왔지만 모종의 사유로 누락되었을 수도 있다.

**최대한 NULL이 존재하지 않는 것이 바람직하다.**

## Keys

### superkey

Relation에서 tuples를 **unique하게 식별할 수 있는 attributes set**

ex) Student의 경우 id도 super key이고, 전체 attributes set도 super key이다.

### candidate key

super key 중 어느 한 attribute라도 제거하면 더 이상 tuples를 unique하게 식별할 수 없는 attributes set

minimal super key

**minimality를 만족한다.**

### primary key (PK)

**relation에서 tuples를 unique하게 식별하기 위해 선택된 candidate key**

보통 여러 candidate key 중 attributes 개수가 최소인 것을 PK로 함

Relation Schema에서 PK는 underbar로 표시한다.

```sql
STUDENT(id, name, grade, major, phone_num, emer_phone_num)
```

### unique key (alternate key)

candidate key 중 PK로 선택받지 못한 key

### foreign key (FK)

다른 relation의 PK를 참조하는 atrributes set

## Constraints

Relational Database의 relations들이 항상 지켜야 하는 제약 사항

### implicit constraints

Relational Data Model 자체가 가지는 Constraints

- **Relation은 중복되는 tuple을 가질 수 없다.**
- **Relation 내에는 중복되는 이름의 attribute를 가질 수 없다.**

### schema-based constraints (explicit constraints)

DDL을 통해 Relation Schema에 직접 명시할 수 있는 constraints

- **Domain constrains**
    
    attribute의 값은 attribute의 domain에 속한 값이어야 한다.
    
- **Key constraints**
    
    서로 다른 tuples는 같은 값의 key를 가질 수 없다.
    
- **NULL value constraint**
    
    어떤 attribute가 NOT NULL로 명시되었다면 NULL 값을 가질 수 없다.
    
- **Entity Intergrity Constraint**
    
    PK는 value에 NULL을 가질 수 없다.
    
- **Referential Integrity Constraint (참조 무결성 제약 조건)**
    - FK와 PK의 domain은 같아야 한다.
    - PK에 없는 value를 FK의 값으로 가질 수 없다.
    
    **PLAYER**
    
    | id | name | team_id | back_number | birth_date |
    | --- | --- | --- | --- | --- |
    | 2023645 | cork | team_387 | 7 | 1999-06-17 |
    | 20161663 | koreii | team_121 | 11 | 1995-10-28 |
    | 20110217 | Jwelyl | team_963 | 89 | 1997-02-13 |
    
    **TEAM**
    
    | id | name | manager |
    | --- | --- | --- |
    | team_387 | protoss | dragoon |
    | team_234 | terran | vulture |
    | team_963 | zerg | hydralisk |
    
    PLAYER relation의 team_id가 TEAM relation의 PK를 참조하고 있으므로 PLAYER relation의 team_id는 FK이다. 그런데 두 번째 tuple의 FK의 값 team_121을 PK로 가지는 tuple이 TEAM relation에 존재하지 않는다. 따라서 참조 무결성 제약 조건을 위반한다.
    

위의 5개의 explicit constraints 중 Domain, Key, Entity Integrity, Referential Integrity Constraints를 **Integrity Constraints**라고 한다.

![Integrity-Constraint.png](24_03_20_daily_certification%205ebf04c29b2c49a2b22c232bcc034b1f/Integrity-Constraint.png)

# Problem Solving (Algorithm & SQL)

**programmers 코딩테스트 연습 2019 KAKAO BLIND RECRUITMENT 후보키**

[](https://school.programmers.co.kr/learn/courses/30/lessons/42890?language=java)

Relational Database의 핵심 개념인 Key에 대해 알아볼 수 있는 문제이다.

relation의 튜플 개수는 최대 20개, 컬럼 개수는 최대 8개이므로 모든 가능한 컬럼 조합의 최대 개수는 8C1 + 8C1 + … + 8C8 - 1 = 2^8 - 1개이다.

컬럼 개수가 적은 조합부터 시작해서 이미 존재하는 후보키와 비교 후 최소성을 만족하는지, 최소성을 만족한다면 유일성도 만족하는지 확인 후 둘 다 만족하면 후보키에 넣어주면 된다.

모든 컬럼을 다 조합해 키로 쓰는 경우도 생각해야 한다. 머리로는 알고 있었지만 조합을 구할 때 c개 중 c개를 고르는 경우를 누락해서 일부 테스트케이스를 통과하지 못했다.

즉 다음과 같은 경우이다.

| column1 | column2 | column3 | column4 |
| --- | --- | --- | --- |
| a | b | c | d |
| x | b | c | d |
| a | x | c | d |
| a | b | x | d |
| a | b | c | x |

위의 경우, 1개 컬럼, 2개 컬럼의 조합, 3개 컬럼의 조합으로는 유일성을 보장할 수 없어서 결국 4개 컬럼 모두를 사용해야 유일성이 보장된다.

조합의 경우의 수를 비트마스킹을 이용해 구현할 수도 있다.

4개의 컬럼이 있을 경우 컬럼을 고르는 경우의 수는 2^4 - 1 = 15가지이다.

각 경우는 이진수로 나타내면 0001, 0010, 0011, 0100, 0101, 0110, 0111, 1000, 1001, 1010, 1011, 1100, 1101, 1110, 1111이므로 1 ~ 15까지를 컬럼의 상태로 보고 비트마스킹을 이용하여 구현하면 아래 코드보다는 깔끔하게 구현이 가능할 것이다.

**코드**

```java
import java.util.*;

class Solution {
    private int t;     //  튜플 개수
    private int c;     //  컬럼 개수
    private Set<String> candKeySet;     //  후보키 집합
    
    public int solution(String[][] relation) {
        t = relation.length;
        c = relation[0].length;
        
        candKeySet = new HashSet<>();   //  후보키 집합
        
        for(int i = 1; i <= c; i++)
            comb(0, i, 0, new int[i], relation);
        
        return candKeySet.size();
    }
    
    //  0, 1, 2, ..., c - 1 중 cnt개를 뽑음
    private void comb(int nth, int cnt, int start, int[] selected, String[][] relation) {
        if(nth == cnt) {    //  cnt개를 모두 뽑았을 경우
            Set<Integer> selectedSet = new HashSet<>(); //  뽑은 컬림들 집합
            for(int i = 0; i < cnt; i++)
                selectedSet.add(selected[i]);
          
            //  1. selected가 minimality를 만족하는지 체크
            boolean minimality = true;
            for(String candKey : candKeySet) {  //  이미 후보키로 확정난 키들을 확인
                List<Integer> candKeyColumnList = new ArrayList<>();
                StringTokenizer tokens = new StringTokenizer(candKey, "#");
                while(tokens.hasMoreTokens())
                    candKeyColumnList.add(Integer.parseInt(tokens.nextToken()));
                
                int dup = 0;    //  이미 후보키로 알려진 키의 모든 column이 현재 selected에 포함됐나 체크
                
                for(int i = 0; i < candKeyColumnList.size(); i++) {
                    int column = candKeyColumnList.get(i);
                    
                    if(selectedSet.contains(column))
                        dup++;
                }
                
                if(dup == candKeyColumnList.size()) { //  만약 후보키의 모든 column이 현재 selected에 포함되면 최소성 위반임
                    minimality = false;
                    break;
                }
            }
            
            if(minimality) {    //  현재 selected가 최소성은 만족할 경우 
                Set<String> tupleSet = new HashSet<String>();
                
                for(int i = 0; i < t; i++) {
                    StringBuilder sb = new StringBuilder();
                    
                    for(int j = 0; j < c; j++) {
                        if(selectedSet.contains(j))
                            sb.append(relation[i][j]).append("#");
                    }
                    
                    tupleSet.add(sb.toString());
                }
                
                if(tupleSet.size() == t) {  //  모든 튜플을 구분할 수 있을 경우 유일성 보장
                    StringBuilder sb = new StringBuilder();
                    for(int i = 0; i < cnt; i++)
                        sb.append(selected[i]).append("#");
                    candKeySet.add(sb.toString());
                }
            }
            
            return;
        }
        
        for(int i = start; i < c; i++) {
            selected[nth] = i;  //  nth번째로 i를 뽑음
            comb(nth + 1, cnt, i + 1, selected, relation);    //  nth+1번째는 i+1부터 뽑을 수 있음
        }
    }
}
```