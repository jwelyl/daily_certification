# 26_01_19_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 30414 투스타 춘배

[30414번: 투스타 춘배](http://boj.ma/30414/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());     //  산의 개수
    int root = Integer.parseInt(st.nextToken());  //  처음 n명의 병사들이 존재하는 산 번호

    int[] diff = new int[n + 1];
    List<Integer>[] tree = new ArrayList[n + 1];
    int[] dp = new int[n + 1];  //  dp[v] : v를 루트로 하는 서브트리에 존재하는 산 높이 맞추기 위해 필요한 최소 비용

    st = new StringTokenizer(br.readLine());
    for(int v = 1; v <= n; v++) {
      tree[v] = new ArrayList<>();
      diff[v] = Integer.parseInt(st.nextToken()); //  v번째 산의 원래 높이
    }
    st = new StringTokenizer(br.readLine());
    for(int v = 1; v <= n; v++)
      diff[v] = Integer.parseInt(st.nextToken()) - diff[v]; //  v번째 산이 원하는 높이가 되기 위해 필요한 추가 높이 (+일 경우 흙을 잃음, -일 경우 흙을 얻음)

    for(int e = 0; e < n - 1; e++) {
      st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      tree[v1].add(v2);
      tree[v2].add(v1);
    }

    dfs(root, diff, tree, new boolean[n + 1], dp);

    System.out.println(Math.max(dp[root], 0));
  } //  main-end

  private static void dfs(int cur, int[] diff, List<Integer>[] tree, boolean[] visited, int[] dp) {
    dp[cur] = diff[cur];
    visited[cur] = true;

    for(int next : tree[cur]) {
      if(!visited[next]) {
        dfs(next, diff, tree, visited, dp);
        dp[cur] += Math.max(dp[next], 0); //  서브트리에서 남아서 저장한 흙을 가져와 쓸 수 없음
      }
    }
  }
} //  Main-class-end
```