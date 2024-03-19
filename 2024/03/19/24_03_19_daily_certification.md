# 24_03_19_daily_certification

# Databse

## 데이터베이스

**전자적으로 저장하고 사용하는 관련 있는(related) 데이터(data)의, 조직화된 집합(organized collection)**

## Database Management System (DBMS)

사용자에게 데이터베이스를 정의하고 만들고 관리하는 기능을 제공하는 소프트웨어 시스템

ex) PostgreSQL, MySQL, ORACLE DATABASE, SQL Server

- 부가적인 데이터(metadata) 발생, 데이터베이스를 정의하거나 기술함(catalog)
    - 데이터 유형, 구조, 제약조건, 보안, 저장, 인덱스, 사용자 그룹 등등
    - metadata도 DBMS에 의해 저장되고 관리됨

## Database System (Database)

Database + DBMS + Application

![Simplified-database-system-Risunok-1-Uprosennaa-sistema-baz-dannyh-Slika.png](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/Simplified-database-system-Risunok-1-Uprosennaa-sistema-baz-dannyh-Slika.png)

## Data Models

DB의 구조를 기술하는데 사용될 수 있는 개념들이 모인 집합

DB 구조를 **추상화해서 표현**할 수 있는 수단을 제공한다.

- DB 구조 : 데이터 유형, 관계(relationship), 제약 사항(constraints) 등등

### **Conceptual Data Models**

일반 사용자들이 쉽게 이해할 수 있는 개념들로 이루어진 모델

추상화 수준이 가장 높음

비즈니스 요구 사항을 추상화하여 기술할 때 사용

- ER diagram
    
    ![ER-Model.png](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/ER-Model.png)
    

### **Logical Data Models**

데이터가 컴퓨터에 저장될 때의 구조와 크게 다르지 않게 DB 구조화가 가능함

특정 DBMS나 storage에 종속되지 않는 수준에서 DB를 구조화

- **Relational Data Model**
    
    ORACLE, MySQL, SQL Server
    
    ![091318_0803_RelationalD1.png](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/091318_0803_RelationalD1.png)
    
- Object Data Model
    
    ![objectDataModel.png](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/objectDataModel.png)
    
- Object-Relational Data Model
    
    PostgreSQL
    
    ![1280px-Object-Oriented_Model.svg.png](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/1280px-Object-Oriented_Model.svg.png)
    

### **Physical Data Models**

컴퓨터에 데이터가 어떻게 파일 형태로 저장되는지를 기술할 수 있는 수단 제공

Data Format, Data Ordering, Access Path 등등

Access Path : 데이터 검색을 빠르게 하기 위한 구조체 ex) index

## Database Schema

Data Model을 기반으로 Database Structure를 기술한 것

Schema는 DB를 설계할 때 정해지며 한 번 저장하면 자주 바뀌지 않음

## Database State

DB의 실제 데이터는 꽤 자주 바뀜

- 특정 시점에 DB에 있는 데이터를 Database State 또는 Snapshot이라고 한다.
- DB에 있는 현재 instance의 집합이다.

![scheme.png](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/scheme.png)

## Three-Schema Architecture

Database System을 구축하는 Architecture 중 하나이자 대표격

**User Application으로부터 Physical Database를 분리시키는 목적**

- Physical DB 구조가 변경되더라도 User Application에는 영향을 주지 않기 위해서

**세 가지 Level이 존재하며 각 Level마다 Schema가 정의되어 있음.**

**어느 레벨에서의 변화가 상위 레벨에 영향을 주지 않기 위해서 사용한다.**

![dbms-three-schema-architecture.png](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/dbms-three-schema-architecture.png)

### Three Level

- **External(View) Level - External Schemas (User Views)**
    - 특정 유저들이 필요로 하는 데이터만 표현한다.
        - 유저마다 필요로 하는 데이터만 보여주고 그 외에는 숨김
        - Logical Data Model을 통해 표현
- **Conceptual Level - Conceptual Schemas**
    - 전체 DB 구조 기술
    - 물리적인 저장 구조 내용은 숨김
    - Entity, Data Types, Relationships, User Operations, Constraints
    - Logical Data Model을 통해 표현
- **Internal Level - Internal Schemas**
    - 물리적 저장 장치에 가장 가까움
    - 물리적으로 데이터가 어떻게 저장되는지 Physical Data Model을 통해 표현
        - Data Storage
        - Data Structure
        - Access Path
    - 실제 데이터가 존재하는 곳

## Definition Language

### Data Definition Language (DDL)

**Conceptual Schemas를 정의하기 위해 사용하는 언어**

Internal Schemas까지 정의할 수 있는 경우도 간혹 존재 (보통 Parameter로 정의)

### Storage Definition Language (SDL)

Internal Schemas를 정의하는 용도로 사용하는 언어 

하지만 보통 RDBMS에는 Paramter 등의 설정으로 대체하여 거의 사용되지 않음

### View Definition Language (VDL)

External Schemas를 정의하기 위해 사용되는 언어

대부분 DBMS에서는 DDL이 VDL 역할까지 수행

## Data Manipulation Language (DML)

DB에 있는 데이터를 활용하기 위한 언어

데이터 추가, 삭제, 수정, 검색 등등의 기능을 제공하는 언어

## SQL

Relational Database Language에서 DDL, VDL, DML을 통합한 언어

# Problem Solving (Algorithm & SQL)

**BOJ 5670 휴대폰 자판**

[5670번: 휴대폰 자판](https://www.acmicpc.net/problem/5670)

트라이라는 것을 모르면 문제만 읽어서는 이해가 어려울 수 있다. 트라이 문제를 골라서 푼 것이기 때문에 트라이를 그림을 그려서 풀어보니 어렵지 않았다.

**예제 1**

![ex1.jpeg](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/ex1.jpeg)

hell/hello와 heaven처럼 he 이후로 분기가 갈라질 경우 추가적으로 한 번 더 입력해야 한다. goodbye처럼 분기가 갈라지지 않을 경우 최초 g만 입력하면 한 번에 goodbye를 찾을 수 있다.

hell/hello의 경우 hell까지는 2번의 입력으로 찾을 수 있지만 hello를 찾기 위해서는 o를 추가로 입력해야 하므로 총 3번의 입력이 필요하다. 어떤 단어를 찾아 중간 노드에 도착했는데, 해당 노드에서 끝나는 단어가 존재할 경우 찾으려는 단어의 다음 노드로 가기 위해서는 한 번의 입력이 더 필요하다.

**예제 2**

![ex2.jpeg](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/ex2.jpeg)

모든 단어가 h로 시작할 경우 h는 입력할 필요 없는거 아닌가 생각할 수 있지만 예제 2에 의하면 h는 한번은 입력을 무조건 해야 한다.

**예제 3**

![ex3.jpeg](24_03_19_daily_certification%20940bb96a6a324c1b895ea83b61b8162b/ex3.jpeg)

아래 코드는 예제 3을 기준으로 구현한 것이다. 예제 1,2에서 언급한 경우를 전부 고려하여 예제 3을 맞출 수 있게 구현하면 별 무리 없이 정답을 받을 수 있다.

**코드**

```java
import java.util.*;
import java.io.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  private final static StringBuilder sb = new StringBuilder();

  private static int n;    //    단어 개수
  private final static List<String> wordList = new ArrayList<String>();    //    단어 리스트
  private static Trie trie;
  private static double cnt = 0;

  public static void main(String[] args) throws IOException {
    while(true) {
      String input = br.readLine();

      if(input == null) {
        System.out.print(sb);
        break;
      }

      n = Integer.parseInt(input);
      trie = new Trie();
      wordList.clear();
      cnt = 0;

      for(int i = 0; i < n; i++) {
        String word = br.readLine();

        wordList.add(word);   //    단어 리스트에 단어 삽입
        trie.insert(word);    //    트라이에 단어 삽입
      }

      for(String word : wordList)    //    단어 리스트에 존재하는 단어 word
        cnt += trie.find(word);    //    word를 검색하기 위해 클릭해야 하는 버튼 수

      sb.append(String.format("%.2f\n", cnt / n));
    }
  }    //   main-end

  private static class TrieNode {
    public Map<Character, TrieNode> children = new HashMap<>();
    public boolean isLast = false;
  }

  private static class Trie {
    private TrieNode root = new TrieNode();

    //    트라이에 단어 word 삽입
    public void insert(String word) {
      TrieNode curNode = root;    //    현재 노드

      for(int i = 0; i < word.length(); i++) {
        char ch = word.charAt(i);    //    단어의 현재 문자
        if(!curNode.children.containsKey(ch))    //    현재 노드의 자식 노드 중 현재 문자에 해당하는 자식 노드가 없을 경우
          curNode.children.put(ch, new TrieNode());    //    현재 문자에 해당하는 자식 노드 생성 후 삽입

        curNode = curNode.children.get(ch);    //    현재 문자에 해당하는 자식 노드를 현재 노드로 함
      }

      curNode.isLast = true;    //    마지막 문자에 해당하는 노드를 마지막 노드로 함
    }

    //    트라이에서 word를 찾고 입력 횟수를 반환함
    public int find(String word) {
      int cnt = 1;    //    최소 한 번은 버튼 클릭해야 함
      TrieNode curNode = root.children.get(word.charAt(0));    //    찾으려는 단어의 첫 번째 문자에 해당하는 노드로 이동

      for(int i = 1; i < word.length(); i++) {
        char nextCh = word.charAt(i);    //    현재 노드에서 찾아야 하는 다음 문자
        TrieNode nextNode = curNode.children.get(nextCh);    //    다음 문자에 해당하는 다음 노드

        if(curNode.children.size() > 1)    //    현재 노드에서 다음 노드까지 한 분기가 아닐 경우
          cnt++;    //    다음 노드로 가기 위해선 한 번 더 클릭해야 함
        else {
          if(i - 1 != word.length() && curNode.isLast)    //    아직 더 가야 하는데 현재 노드는 끝 노드일 경우
            cnt++;    //    다음 노드로 가기 위해선 한 번 더 클릭해야 함
        }

        curNode = nextNode;    //    다음 노드로 이동
      }

      return cnt;
    }
  }
}   //   Main-class-end
```

애초에 트라이 문제를 골라서 푼 것이기 때문에 트라이로 접근했지만, 백준이 아니라 실제 시험에서 이 문제를 만났다면 트라이를 떠올릴 수 있을지 모르겠다. 실제 시험에서도 트라이를 떠올리려면 그만큼 트라이 관련 문제를 많이 풀어봐야겠지.