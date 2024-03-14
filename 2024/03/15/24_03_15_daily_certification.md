# 24_03_15_daily_certification

# Problem Solving (Algorithm & SQL)

**BOJ 1414 불우이웃돕기**

[1414번: 불우이웃돕기](https://www.acmicpc.net/problem/1414)

n개의 정점을 가진 그래프가 인접 행렬 형태로 주어진다. 특이 사항으로 간선 비용이 문자로 주어진 것, 그리고 자기 자신으로 가는 간선도 주어졌으며 단방향 그래프라는 말은 없지만 graph[v1][v2]와 graph[v2][v1]이 둘 다 주어지는 경우가 있다는 것이다.

예제 1을 그림으로 나타내면 다음과 같다.

![ex1.jpeg](24_03_15_daily_certification%20430ec860ca81411ba1a03021cd4f187a/ex1.jpeg)

문제의 조건에 의하면 모든 정점이 이어지도록 최소 크기 간선만 남기고 나머지는 기부하는 것이다. 즉 최소 비용 신장 트리를 만들고, 원래 그래프의 간선에서 MST에 포함되지 않는 간선들을 기부하면 되는 것이다.

![ex1_1.jpeg](24_03_15_daily_certification%20430ec860ca81411ba1a03021cd4f187a/ex1_1.jpeg)

최소 비용 신장 트리를 만드는데 고려할 필요가 없는 간선들을 점선으로 표시해봤다. 자기 자신과 연결되는 간선은 최소 비용 신장 트리를 만드는데 전혀 도움이 되지 않는다. 또한 두 정점 사이에 간선이 여러 개일 경우, 가중치가 작은 간선만 1개 놔두면 된다. 위의 그림에서 실선으로 된 간선이 후보 간선이다. 후보 간선만 가지고 Kruskal Algorithm을 적용하여 mst의 비용을 구하고 전체 간선 비용에서 빼주면 된다.

그래프 구축 시 주의 사항으로 graph[v1][v2] == ‘0’이면 v1 - v2 사이 간선이 없다는 뜻이다. 예외 처리를 해서 간선을 추가하지 않던, Integer.MAX_VALUE 값을 주던, 0으로 계산하는 실수를 해서는 안된다.

**실패한 코드 (틀렸습니다)**

```java
import java.io.*;
import java.util.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static int n;    //    방 개수

  private static char[][] graph;
  private static int[] parents;    //    parents[i] : i번째 정점의 부모
  private static int total = 0;    //    mst 만들기 전 간선 총 합

  private static List<Edge> edgeList = new ArrayList<>();

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    graph = new char[n + 1][n + 1];
    parents = new int[n + 1];
    for(int i = 1; i <= n; i++)
      parents[i] = i;

    for(int i = 1; i <= n; i++) {
      String input = br.readLine();
      for(int j = 1; j <= n; j++) {
        graph[i][j] = input.charAt(j - 1);
        total += costOfEdge(graph[i][j]);
      }
    }

    //    자기 자신과 연결된 간선은 어차피 필요 없으므로 서로 다른 두 정점 v1, v2 연결 간선만 확인
    for(int v1 = 1; v1 <= n - 1; v1++) {
      for(int v2 = v1 + 1; v2 <= n; v2++) {
        int costV1V2 = costOfEdge(graph[v1][v2]);
        int costV2V1 = costOfEdge(graph[v2][v1]);
        int cost = 0;

        if(costV1V2 == 0 && costV2V1 == 0)    //    v1, v가 연결되지 않았을 경우
          continue;
        else if(costV1V2 == 0)
          cost = costV2V1;
        else if(costV2V1 == 0)
          cost = costV1V2;
        else
          cost = Math.min(costV1V2, costV2V1);

        edgeList.add(new Edge(v1, v2, cost));
      }
    }

    if(edgeList.size() < n - 1)    //    연결이 불가할 경우
      System.out.println(-1);
    else
      System.out.println(total - kruskal());
  }   //  main-end

  private static int kruskal() {
    int mstCost = 0;
    int mstEdgeCnt = 0;
    Collections.sort(edgeList);

    for(Edge edge : edgeList) {
      int v1 = edge.v1;
      int v2 = edge.v2;
      int cost = edge.cost;

      if(findDS(v1) != findDS(v2)) {
        unionDS(v1, v2);
        mstCost += cost;
        mstEdgeCnt++;
      }

      if(mstEdgeCnt == n - 1)
        break;
    }

    return mstCost;
  }

  private static int findDS(int v) {
    if(v == parents[v])
      return v;

    return parents[v] = findDS(parents[v]);
  }

  private static void unionDS(int v1, int v2) {
    v1 = findDS(v1);
    v2 = findDS(v2);

    if(v1 != v2)
      parents[v2] = v1;
  }

  private static int costOfEdge(char ch) {
    if(ch == '0')
      return 0;
    else if('a' <= ch && ch <= 'z')
      return (ch - 'a') + 1;
    else
      return (ch - 'A') + 27;
  }

  private static class Edge implements Comparable<Edge> {
    final int v1;
    final int v2;
    final int cost;

    public Edge(int v1, int v2, int cost) {
      this.v1 = v1;
      this.v2 = v2;
      this.cost = cost;
    }

    @Override
    public int compareTo(Edge edge) {
      return Integer.compare(this.cost, edge.cost);
    }
  }
}   //  Main-class-end
```

대부분의 예제를 통과했지만 다음과 같은 그래프가 만들어질 경우 통과하지 못한다. 간선 비용은 중요하지 않으므로 생략한다.

![counter.jpeg](24_03_15_daily_certification%20430ec860ca81411ba1a03021cd4f187a/counter.jpeg)

정점이 7개고 간선도 9개이므로 간선 개수가 6개보다 많기 때문에 아래 첫 번째 조건문을 무사히 통과한다.

```java
if(edgeList.size() < n - 1)    //    연결이 불가할 경우
  System.out.println(-1);
else
  System.out.println(total - kruskal());
```

하지만 Kruskal Algortihm에서 문제가 발생한다.

```java
private static int kruskal() {
  int mstCost = 0;
  int mstEdgeCnt = 0;
  Collections.sort(edgeList);

  for(Edge edge : edgeList) {
    int v1 = edge.v1;
    int v2 = edge.v2;
    int cost = edge.cost;

    if(findDS(v1) != findDS(v2)) {
      unionDS(v1, v2);
      mstCost += cost;
      mstEdgeCnt++;
    }

    if(mstEdgeCnt == n - 1)
      break;
  }

  return mstCost;
}
```

Kruskal Algorithm에서 mst 간선 개수가 n - 1개가 되면 반복문을 멈추도록 했지만, n - 1개가 되지 못한 상태에서 반복문이 종료되는 경우, 즉 위의 그래프처럼 연결 그래프가 아닐 경우 MST가 만들어지지 못하는데 그에 대한 예외처리가 되어있지 않다.

따라서 예외처리를 해야한다.

**성공한 코드**

```java
import java.io.*;
import java.util.*;

public class Main {
  private final static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static int n;    //    방 개수

  private static char[][] graph;
  private static int[] parents;    //    parents[i] : i번째 정점의 부모
  private static int total = 0;    //    mst 만들기 전 간선 총 합

  private static List<Edge> edgeList = new ArrayList<>();

  public static void main(String[] args) throws IOException {
    n = Integer.parseInt(br.readLine());

    graph = new char[n + 1][n + 1];
    parents = new int[n + 1];
    for(int i = 1; i <= n; i++)
      parents[i] = i;

    for(int i = 1; i <= n; i++) {
      String input = br.readLine();
      for(int j = 1; j <= n; j++) {
        graph[i][j] = input.charAt(j - 1);
        total += costOfEdge(graph[i][j]);
      }
    }

    //    자기 자신과 연결된 간선은 어차피 필요 없으므로 서로 다른 두 정점 v1, v2 연결 간선만 확인
    for(int v1 = 1; v1 <= n - 1; v1++) {
      for(int v2 = v1 + 1; v2 <= n; v2++) {
        int costV1V2 = costOfEdge(graph[v1][v2]);
        int costV2V1 = costOfEdge(graph[v2][v1]);
        int cost = 0;

        if(costV1V2 == 0 && costV2V1 == 0)    //    v1, v가 연결되지 않았을 경우
          continue;
        else if(costV1V2 == 0)
          cost = costV2V1;
        else if(costV2V1 == 0)
          cost = costV1V2;
        else
          cost = Math.min(costV1V2, costV2V1);

        edgeList.add(new Edge(v1, v2, cost));
      }
    }
    
    int kruskal = kruskal();

    if (kruskal == -1)
       System.out.println(-1);
    else
       System.out.println(total - kruskal);
  }   //  main-end

  private static int kruskal() {
    int mstCost = 0;                //   mst 비용
    int mstEdgeCnt = 0;             //   mst 간선 개수
    Collections.sort(edgeList);     //   간선 크기 순으로 정렬

    for(Edge edge : edgeList) {
      int v1 = edge.v1;
      int v2 = edge.v2;
      int cost = edge.cost;

      if(findDS(v1) != findDS(v2)) {    //    아직 두 정점이 연결되지 않은 경우
        unionDS(v1, v2);    //    간선 추가
        mstCost += cost;    //    mst 비용에 추가
        mstEdgeCnt++;       //    간선 개수 1 증가
      }

      if(mstEdgeCnt == n - 1)    //    mst가 완성된 경우
        break;
    }

    if(mstEdgeCnt < n - 1)    //    연결 그래프가 아니라서 mst가 완성될 수 없는 경우
      return -1;

    return mstCost;
  }

  private static int findDS(int v) {
    if(v == parents[v])
      return v;

    return parents[v] = findDS(parents[v]);
  }

  private static void unionDS(int v1, int v2) {
    v1 = findDS(v1);
    v2 = findDS(v2);

    if(v1 != v2)
      parents[v2] = v1;
  }

  private static int costOfEdge(char ch) {
    if(ch == '0')
      return 0;
    else if('a' <= ch && ch <= 'z')
      return (ch - 'a') + 1;
    else
      return (ch - 'A') + 27;
  }

  private static class Edge implements Comparable<Edge> {
    final int v1;
    final int v2;
    final int cost;

    public Edge(int v1, int v2, int cost) {
      this.v1 = v1;
      this.v2 = v2;
      this.cost = cost;
    }

    @Override
    public int compareTo(Edge edge) {
      return Integer.compare(this.cost, edge.cost);
    }
  }
}   //  Main-class-end
```

Kruskal Algorithm 코드에 MST가 완성될 수 없는 경우에 대한 예외 처리를 추가했다.

```java
private static int kruskal() {
  int mstCost = 0;                //   mst 비용
  int mstEdgeCnt = 0;             //   mst 간선 개수
  Collections.sort(edgeList);     //   간선 크기 순으로 정렬

  for(Edge edge : edgeList) {
    int v1 = edge.v1;
    int v2 = edge.v2;
    int cost = edge.cost;

    if(findDS(v1) != findDS(v2)) {    //    아직 두 정점이 연결되지 않은 경우
      unionDS(v1, v2);    //    간선 추가
      mstCost += cost;    //    mst 비용에 추가
      mstEdgeCnt++;       //    간선 개수 1 증가
    }

    if(mstEdgeCnt == n - 1)    //    mst가 완성된 경우
      break;
  }

  if(mstEdgeCnt < n - 1)    //    연결 그래프가 아니라서 mst가 완성될 수 없는 경우
    return -1;

  return mstCost;
}
```