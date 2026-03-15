# 26_03_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1765 닭싸움 팀 정하기

[1765번: 닭싸움 팀 정하기](http://boj.ma/1765/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  private static int teamCnt;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());
    int m = Integer.parseInt(br.readLine());

    List<Integer>[] graph = new ArrayList[n + 1];   //	적대 관계 그래프
    List<int[]> friendship = new ArrayList<>();     //  친구 관계

    boolean[] visited = new boolean[n + 1];
    int[] parents = new int[n + 1];

    teamCnt = n;

    for(int v = 1; v <= n; v++) {
      graph[v] = new ArrayList<>();
      parents[v] = v;
    }

    for(int e = 0; e < m; e++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      char r = st.nextToken().charAt(0);
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());

      if(r == 'E') {	//	적대 관계일 경우
        graph[v1].add(v2);
        graph[v2].add(v1);
      }
      else
        friendship.add(new int[] {v1, v2});
    }

    for(int v = 1; v <= n; v++) {
      if(!visited[v]) {
        List<Integer>[] teams = new ArrayList[2];
        teams[0] = new ArrayList<>();
        teams[1] = new ArrayList<>();

        dfs(v, 0, graph, teams, visited);

        union(teams[0], parents);
        union(teams[1], parents);
      }
    }

    for(int[] r : friendship) {
      int v1 = r[0];
      int v2 = r[1];

      union(v1, v2, parents);
    }

    System.out.println(teamCnt);
  } //	main-end

  private static void dfs(int v, int teamIdx, List<Integer>[] graph, List<Integer>[] teams, boolean[] visited) {
    visited[v] = true;
    teams[teamIdx].add(v);

    for(int nv : graph[v]) {
      if(!visited[nv])
        dfs(nv, 1 - teamIdx, graph, teams, visited);
    }
  }

  private static int find(int v, int[] parents) {
    if(v == parents[v])
      return v;

    return parents[v] = find(parents[v], parents);
  }

  private static void union(int v1, int v2, int[] parents) {
    v1 = find(v1, parents);
    v2 = find(v2, parents);

    if(v1 != v2) {
      teamCnt--;
      parents[v2] = v1;
    }
  }

  private static void union(List<Integer> team, int[] parents) {
    if(team.size() >= 2) {
      for(int i = 1; i < team.size(); i++) {
        int v1 = team.get(i - 1);
        int v2 = team.get(i);

        union(v1, v2, parents);
      }
    }
  }
} //	Main-class-end
```