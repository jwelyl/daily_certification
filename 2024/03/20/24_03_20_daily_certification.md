# 24_03_20_daily_certification

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

[](https://school.programmers.co.kr/learn/courses/30/lessons/42890?language=java)

```java

```