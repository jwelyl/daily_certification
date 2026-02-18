# 26_02_19_daily_certification

```
[#050 koreii] 데일리인증 20260219
1 알고리즘 문제 풀이
- Graph, Greedy, MST (BOJ 1045 도로)
```

# Problem Solving (Algorithm & SQL)

### BOJ 1045 도로

[1045번: 도로](http://boj.ma/1045/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken()); //    도시 개수
    int m = Integer.parseInt(st.nextToken()); //    목표하는 도로 집합 크기

    char[][] graph;    //    도시 간 연결 상태
    int[] parents;

    List<Edge> edgeList = new ArrayList<>();
    int[] cnts;

    if(m < n - 1) {    //    n - 1개보다 적은 도로로는 연결된 집합을 만들 수 없음
      System.out.println(-1);
      return;
    }

    graph = new char[n][n];
    parents = new int[n];
    cnts = new int[n];

    for(int v1 = 0; v1 < n; v1++) {
      graph[v1] = br.readLine().toCharArray();
      parents[v1] = v1;

      for(int v2 = v1 + 1; v2 < n; v2++) {
        if(graph[v1][v2] == 'Y')
          edgeList.add(new Edge(v1, v2));
      }
    }

    kruskal(n, m, edgeList, parents, cnts);
  }    //    main-end

  private static void kruskal(int n, int m, List<Edge> edgeList, int[] parents, int[] cnts) {
    StringBuilder sb = new StringBuilder();
    int edgeCnt = 0;
    List<Edge> edgeSet = new ArrayList<>();

    //    일단 MST를 만들어 연결상태 만들기
    for(Edge edge : edgeList) {
      int v1 = edge.v1;
      int v2 = edge.v2;

      if(find(v1, parents) != find(v2, parents)) {
        union(v1, v2, parents);
        edgeCnt++;
        edge.used = true;
        edgeSet.add(edge);

        if(edgeCnt == n - 1)
          break;
      }
    }

    if(edgeCnt < n - 1) {   //    연결 상태를 만들지 못할 경우
      System.out.println(-1);
      return;
    }

    //    이미 연결된 상태에서 M개 도로 채우기 위해 추가 도로 확인
    if(edgeCnt < m) {
      for(Edge edge : edgeList) {
        if(edge.used)
          continue;

        edgeSet.add(edge);
        edgeCnt++;

        if(edgeCnt == m)
          break;
      }
    }

    if(edgeCnt < m)
      System.out.println(-1);
    else {
      for(Edge edge : edgeSet) {
        int v1 = edge.v1;
        int v2 = edge.v2;

        cnts[v1]++;
        cnts[v2]++;
      }

      for(int i = 0; i < n; i++)
        sb.append(cnts[i]).append(" ");

      System.out.println(sb);
    }
  }

  private static int find(int v, int[] parents) {
    return v == parents[v] ? v : (parents[v] = find(parents[v], parents));
  }

  private static void union(int v1, int v2, int[] parents) {
    v1 = find(v1, parents);
    v2 = find(v2, parents);

    if(v1 != v2)
      parents[v1] = v2;
  }

  private static class Edge {
    public int v1;
    public int v2;
    public boolean used;

    public Edge(int v1, int v2) {
      this.v1 = v1;
      this.v2 = v2;
      this.used = false;
    }
  }
}    //    Main-class-end
```