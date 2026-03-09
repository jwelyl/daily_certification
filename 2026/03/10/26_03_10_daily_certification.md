# 26_03_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10273 **고대 동굴 탐사**

[10273번: 고대 동굴 탐사](http://boj.ma/10273/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
import java.util.Stack;

public class Main {
  private static final int ROOT = 1;
  private static final int NONE = 0;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());  //    테스트케이스

    for(int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int n = Integer.parseInt(st.nextToken());   //    동굴 수
      int e1 = Integer.parseInt(st.nextToken());  //    동굴 사이 통로 수

      int[] values = new int[n + 1];    //    values[v] : v번째 동굴 가치
      List<int[]>[] graph = new ArrayList[n + 1];

      int[] parents = new int[n + 1];
      int[] dp = new int[n + 1];        //    dp[v] : v번째 동굴까지 왔을떄 최대 가치

      st = new StringTokenizer(br.readLine());
      for(int v = 1; v <= n; v++) {
        values[v] = Integer.parseInt(st.nextToken());
        graph[v] = new ArrayList<>();
        dp[v] = Integer.MIN_VALUE;
      }

      for(int e = 0; e < e1; e++) {
        st = new StringTokenizer(br.readLine());
        int v1 = Integer.parseInt(st.nextToken());
        int v2 = Integer.parseInt(st.nextToken());
        int c = Integer.parseInt(st.nextToken());

        graph[v1].add(new int[] {v2, c});
      }

      bfs(graph, values, dp, parents, sb);
    }

    System.out.print(sb);
  }    //    main-end

  private static void bfs(List<int[]>[] graph, int[] values, int[] dp, int[] parents, StringBuilder sb) {
    Queue<Integer> queue = new LinkedList<>();
    int maxProfit = 0;                        //    최대 가치
    int maxProfitV = NONE;                    //    최대 가치가 되는 도착 동굴
    Stack<Integer> path = new Stack<>();      //    그때 동굴 경로

    dp[ROOT] = values[ROOT];
    maxProfit = dp[ROOT];
    maxProfitV = ROOT;
    queue.offer(ROOT);

    while(!queue.isEmpty()) {
      int cur = queue.poll();

      for(int[] edge : graph[cur]) {
        int next = edge[0];
        int cost = edge[1];

        if(dp[cur] - cost + values[next] > dp[next]) {
          dp[next] = dp[cur] - cost + values[next];
          parents[next] = cur;
          queue.offer(next);

          if(dp[next] > maxProfit) {
            maxProfit = dp[next];
            maxProfitV = next;
          }
        }
      }
    }

    while(maxProfitV != NONE) {
      path.add(maxProfitV);
      maxProfitV = parents[maxProfitV];
    }

    sb.append(maxProfit).append(" ").append(path.size()).append("\n");
    while(!path.isEmpty())
      sb.append(path.pop()).append(" ");
    sb.append("\n");
  }
}    //    Main-class-end
```