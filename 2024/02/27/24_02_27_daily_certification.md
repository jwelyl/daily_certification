# 24_02_27_daily_certification

# Database

## JOIN

다음과 같은 두 테이블이 존재할 때

**employees**

| employee_id | employee_name | department_id |
| --- | --- | --- |
| 1 | John Doe | 1 |
| 2 | Jane Smith | 2 |
| 3 | Anne Brown | 3 |
| 4 | Bill Green | 4 |

**departments**

| department_id | department_name |
| --- | --- |
| 1 | HR |
| 2 | IT |
| 3 | Marketing |

### Inner Join

두 테이블의 교집합만을 결과로 반환한다. 즉, 두 테이블 모두에 해당하는 행만을 결과로 가져온다.

department_id를 기준으로 두 테이블을 inner join으로 결합하면 다음과 같은 결과 테이블을 얻는다.

| employee_id | employee_name | department_id | department_name |
| --- | --- | --- | --- |
| 1 | John Doe | 1 | HR |
| 2 | Jane Smith | 2 | IT |
| 3 | Anne Brown | 3 | Marketing |

employees 테이블에서 department_id = 4인 행이 departments 테이블에서는 존재하지 않으므로 결과 테이블에서 Bill Green의 정보는 나타나지 않는다.

**SQL**

```sql
SELECT * FROM employees INNER JOIN departments 
ON employees.department_id = departments.department_id;
```

### Left (Outer) Join

왼쪽 테이블(employees)의 모든 행을 포함하며, 오른쪽 테이블(departmenst)에서 일치하는 행이 없는 경우 해당 값을 NULL로 가진다.

department_id를 기준으로 두 테이블을 left outer join으로 결합하면 다음과 같은 결과 테이블을 얻는다.

| employee_id | employee_name | department_id | department_name |
| --- | --- | --- | --- |
| 1 | John Doe | 1 | HR |
| 2 | Jane Smith | 2 | IT |
| 3 | Anne Brown | 3 | Marketing |
| 4 | Bill Green | 4 | NULL |

employees 테이블에서 department_id = 4인 행이 department 테이블에서는 존재하지 않으므로 결과 테이블에서 Bill Green의 department_name은 NULL이 된다.

**SQL**

```sql
SELECT * FROM employees LEFT OUTER JOIN departments 
ON employees.department_id = departments.department_id;
```

### Theta Join

두 테이블의 JOIN 조건으로 특정 조건(<, >, =, ≠)을 사용한다. 명시적으로 theta join이라고 표현하지는 않는 대신, on 키워드로 join 조건을 지정한다.

department_id를 기준으로 두 테이블을 join하되 employee_id가 2보다 큰 직원만 결과에 포함되도록 하면 결과 테이블은 다음과 같다.

| employee_id | employee_name | department_id | department_name |
| --- | --- | --- | --- |
| 3 | Anne Brown | 3 | Marketing |

기본적으로 inner join에 추가적인 조건을 on으로 명시하는 것과 같다. department_id로 inner join한 결과에 employee_id 조건을 추가했다.

**SQL**

```sql
SELECT * FROM employees INNER JOIN departments 
ON employees.department_id = departments.department_id
where employees.emplyee_id > 2;
```

department_id가 departments 테이블에 존재하지 않는 직원 결과도 나오도록 하면 결과 테이블은 다음과 같다.

| employee_id | employee_name | department_id | department_name |
| --- | --- | --- | --- |
| 3 | Anne Brown | 3 | Marketing |
| 4 | Bill Green | 4 | NULL |

기본적으로 left outer join에 추가적인 조건을 on으로 명시하는 것과 같다. department_id로left outer join한 결과에 employee_id 조건을 추가했다.

**SQL**

```sql
SELECT * FROM employees LEFT OUTER JOIN departments 
ON employees.department_id = departments.department_id
where employees.emplyee_id > 2;
```

실제로 그럴 일은 거의 없겠지만 employees 테이블의 employee_name 값과 departments 테이블의 department_name을 비교하여 둘이 같은 값을 가지는 row를 결합하고 싶을 수도 있다. 이런 근본 없는(즉 FK가 아닌 값으로 JOIN하는) JOIN이 보통 말하는 theta join이다.

예시를 위해 테이블을 수정해보자.

**employees**

| employee_id | employee_name | department_id |
| --- | --- | --- |
| 1 | Engineering | 1 |
| 2 | HR | 2 |
| 3 | Marketing | 3 |
| 4 | Design | 4 |

**departments**

| department_id | department_name |
| --- | --- |
| 1 | Engineering |
| 2 | HR |
| 3 | Marketing |

employee_name과 departmnet_name으로 inner join하는 SQL문과 결과는  다음과 같다.

**SQL**

```sql
SELECT * FROM employees INNER JOIN departments 
ON employees.employee_name = departments.department_name;
```

| employee_id | employee_name | department_id | department_name |
| --- | --- | --- | --- |
| 1 | Engineering | 1 | Engineering |
| 2 | HR | 2 | HR |
| 3 | Marketing | 3 | Marketing |

employee_name과 departmnet_name으로 left outer join하는 SQL문과 결과는  다음과 같다.

**SQL**

```sql
SELECT * FROM employees LEFT JOIN departments 
ON employees.employee_name = departments.department_name;
```

| employee_id | employee_name | department_id | department_name |
| --- | --- | --- | --- |
| 1 | Engineering | 1 | Engineering |
| 2 | HR | 2 | HR |
| 3 | Marketing | 3 | Marketing |
| 4 | Design | 4 | NULL |

### Natural Join

Inner Join이되, JOIN 조건을 생략한 경우이다. JOIN할 두 테이블이 공통적으로 같은 column 명, 같은 타입 값을 가지면 on으로 JOIN 조건을 생략할 수 있다.

**employees**

| employee_id | employee_name | department_id |
| --- | --- | --- |
| 1 | John Doe | 1 |
| 2 | Jane Smith | 2 |
| 3 | Anne Brown | 3 |
| 4 | Bill Green | 4 |

**departments**

| department_id | department_name |
| --- | --- |
| 1 | HR |
| 2 | IT |
| 3 | Marketing |

두 테이블 모두 department_id라는 column을 공통으로 가지고, 타입도 같으므로 natural join 시 department_id로 inner join이 된다.

**SQL**

```sql
SELECT * FROM employees NATURAL JOIN departments;
```

| employee_id | employee_name | department_id | department_name |
| --- | --- | --- | --- |
| 1 | John Doe | 1 | HR |
| 2 | Jane Smith | 2 | IT |
| 3 | Anne Brown | 3 | Marketing |

만약 다음과 같이 두 테이블에 공통되는 column이 두 개(department_id, leader_id) 이상일 경우

**employees**

| employee_id | employee_name | department_id | leader_id |
| --- | --- | --- | --- |
| 1 | Alice | 101 | 1 |
| 2 | Bob | 102 | 2 |
| 3 | Charlie | 103 | 3 |
| 4 | David | 101 | 4 |

**projects 테이블**

| project_id | project_name | department_id | leader_id |
| --- | --- | --- | --- |
| P1 | Project A | 101 | 1 |
| P2 | Project B | 102 | 2 |
| P3 | Project C | 101 | 1 |
| P4 | Project D | 104 | 5 |
| P5 | Project E | 105 | 6 |

공통되는 column(department_id, leader_id)이 모두 같은 행만 natural join 결과로 나온다.

| employee_id | employee_name | department_id | leader_id | project_id | project_name |
| --- | --- | --- | --- | --- | --- |
| 1 | Alice | 101 | 1 | P1 | Project A |
| 1 | Alice | 101 | 1 | P3 | Project C |
| 2 | Bob | 102 | 2 | P2 | Project B |

Natural Join은 SQL문이 간결해지고 작성하기 편리하지만 JOIN 조건이 불명확해진다. 이로 인해 SQL만 보고 의도를 파악하기 어려워져서 유지 보수가 어려우며, 특정 column으로 JOIN할 수도 없고 스키마 변경 시 오류가 발생하거나 결과가 달라질 수도 있다.

따라서 가급적이면 JOIN 조건을 명확히 명시하는 것이 좋다.

# Problem Solving (Algorithm & SQL)

**BOJ 17940 일감호에 다리 놓기**

[17490번: 일감호에 다리 놓기](https://www.acmicpc.net/problem/17490)

일단 공사중인(편의상 부서진) 구간 개수 M이 1 이하면 와우도(N + 1)과 연결하지 않고도 강의동 1~N은 연결되어 있다. 따라서 K의 개수에 관계 없이 항상 YES이다.

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled.png)

M = 0인 경우 이미 연결되어 있다.

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled%201.png)

M = 1인 경우 N개의 구간 중 어느 하나가 부서졌다 쳐도 나머지 N-1개의 구간으로 연결되어 있다.

따라서 M이 2 이상인 경우에만 생각하면 된다.

예제 1, 2번의 상황은 다음 그림과 같다.

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled%202.png)

부서진 구간은 점선으로 표시했고 멀쩡한 구간은 빨간색 실선으로 표시했다. 와우도와 연결하는 선은 파란색으로 표시했다. 간선의 숫자는 필요한 돌 개수이다. 멀쩡한 구간에는 돌이 필요없다.

2개 이상의 구간이 부서진 이상 1~N 사이 간선만으로는 1~N을 전부 연결할 수 없다. 따라서 반드시 N+1을 통해 연결되어야 한다. 즉, N+1개의 정점을 연결하는 최소 비용 신장 트리를 구축해야 한다.

간선을 구현하기 위해 Edge 클래스를 구현했다. 

Section 클래스는 간선의 두 정점 정보가 (번호가 작은 정점, 번호가 큰 정점) 형태로 저장되어 있다.

부서진 간선 정보는 broken이라는 HashSet에 간선 구간을 Section으로 저장한다.

2N개의 간선을 모두 만들고, 각각의 간선을 broken에서 검색하여, broken에 포함되어 있으면 사용할 수 없는 간선이므로 include를 false로 처리한다. (아마 이 부분이 시간이 오래 걸리는 것 같다.)

모든 간선의 include를 확정한 후에는 Kruskal Algorithm으로 최소 비용 신장 트리의 cost를 구하면 된다. include가 false인 간선은 우선적으로 제외해야 한다.

어차피 간선들은 크기 순으로 정렬되므로 부서지지 않은 간선들이 우선되게 된다. 해당 간선들을 전부 연결해도 사이클이 발생하지 않으므로 반드시 MST에 포함된다. 따라서 별도 처리를 해주지 않아도 된다. 

![Untitled](24_02_27_daily_certification%20326274df432f41429e5c1ed6c0cbfbd4/Untitled%203.png)

MST의 cost를 구한 후 K와 비교해서 K보다 작거나 같으면 YES를, 아니면 NO를 출력한다.

### 코드 (성공했지만 많이 느림)

```kotlin
import java.util.*
import java.io.*

class BOJ_17490 {
    private val br = BufferedReader(InputStreamReader(System.`in`))
    private val sb = StringBuilder()
    private lateinit var tokens: StringTokenizer

    private var n = 0   //  강의동 개수 (1 ~ n : 강의동 번호, n + 1 : 와우도)
    private var m = 0   //  공사 구간 개수
    private var k = 0L   //  돌 개수

    private val brokens = HashSet<Section>()    //  부서진 구간 저장

    private lateinit var ds : IntArray  //  ds[i] : i가 속한 분리집합의 우두머리

    private val edgeList = ArrayList<Edge>()    //  간선 저장 리스트

    fun solve() {
        tokens = StringTokenizer(br.readLine())
        n = tokens.nextToken().toInt()
        m = tokens.nextToken().toInt()
        k = tokens.nextToken().toLong()

        if(m <= 1)  //  부서진 구간이 1개 이하면 이미 강의동끼리 연결되어 있음
            println("YES")
        else {  //  부서진 구간이 2개 이상이면 반드시 와우도랑 연결해야 함
            tokens = StringTokenizer(br.readLine())

            //  각 강의동과 와우도까지 연결 비용
            for(i in 1 .. n)
                edgeList.add(Edge(Section(i, n + 1), tokens.nextToken().toInt(), true))

            //  각 강의동끼리 연결
            for(i in 1..< n)
                edgeList.add(Edge(Section(i, i + 1), 0, true))
            edgeList.add(Edge(Section(1, n), 0, true))

            //  부서진 구간 정보 받기
            repeat(m) {
                tokens = StringTokenizer(br.readLine())
                val v1 = tokens.nextToken().toInt()
                val v2 = tokens.nextToken().toInt()

                brokens.add(Section(v1.coerceAtMost(v2), v1.coerceAtLeast(v2)))
            }

            var brokenCnt = 0  // 부서진 간선 찾은 개수

            for(edge in edgeList) {
                val section = edge.section

                if (brokens.contains(section)) {  //  현재 간선이 부서진 구간이면
                    edge.include = false    //  간선이 포함될 수 없도록 표시한다.
                    brokenCnt++
                }

                if(brokenCnt == m)  //  부서진 간선을 모두 찾았을 경우
                    break
            }

            ds = IntArray(n + 2) { it }

            println(if(kruskal() > k) "NO" else "YES")
        }
    }

    private fun kruskal() : Long {
        var mstCost = 0L
        var mstEdgeCnt = 0

        edgeList.sort()

        for(edge in edgeList) {
            if(!edge.include)   //  부서진 구간 간선이면
                continue

            val v1 = edge.section.v1
            val v2 = edge.section.v2
            val v1DS = findDS(v1)
            val v2DS = findDS(v2)

            if(v1DS != v2DS) {
                unionDS(v1DS, v2DS)
                mstEdgeCnt++
                mstCost += edge.cost
            }

            if(mstEdgeCnt == n)
                break
        }

        return mstCost
    }

    private fun findDS(v : Int) : Int {
        if(v == ds[v])
            return v

        ds[v] = findDS(ds[v])
        return ds[v]
    }

    private fun unionDS(v1 : Int, v2 : Int) {
        val v1DS = findDS(v1)
        val v2DS = findDS(v2)

        if(v1DS != v2DS)
            ds[v2DS] = v1DS
    }

    private data class Section(val v1 : Int, val v2 : Int)

    private class Edge(val section: Section, val cost : Int, var include : Boolean) : Comparable<Edge> {
        override fun compareTo(other : Edge) : Int {
            return this.cost.compareTo(other.cost)
        }
    }
}

fun main() {
    BOJ_17490().solve()
}
```

간선을 구축하고 부서진 간선을 구별하는 더 좋은 방법을 생각해봐야겠다.

| employee_id | employee_name | department_id |
| --- | --- | --- |
| 1 | John Doe | 1 |
| 2 | Jane Smith | 2 |
| 3 | Anne Brown | 3 |