# 26_03_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 24461 그래프의 줄기

[](http://boj.ma/24461/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    List<Integer>[] tree = new ArrayList[n];
    int[] indegrees = new int[n];
    boolean[] deleted = new boolean[n];

    for (int v = 0; v < n; v++)
      tree[v] = new ArrayList<>();

    for (int e = 0; e < n - 1; e++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int v1 = Integer.parseInt(st.nextToken());
      int v2 = Integer.parseInt(st.nextToken());
      tree[v1].add(v2);
      tree[v2].add(v1);
      indegrees[v1]++;
      indegrees[v2]++;
    }

    topologicalSort(n, tree, indegrees, deleted);
  }	//	main-end

  private static void topologicalSort(int n, List<Integer>[] tree, int[] indegrees, boolean[] deleted) {
    Queue<Integer> queue = new ArrayDeque<>();
    StringBuilder sb = new StringBuilder();

    for (int v = 0; v < n; v++) {
      if (indegrees[v] == 1) {
        queue.add(v);
        deleted[v] = true;
      }
    }

    while (true) {
      int size = queue.size();
      if (size <= 2)	//	줄기만 남았으면
        break;

      while(size-- > 0) {
        int cv = queue.poll();
        indegrees[cv]--;
        for (int nv : tree[cv]) {
          if (!deleted[nv]) {
            indegrees[nv]--;

            if(indegrees[nv] == 1) {
              deleted[nv] = true;
              queue.add(nv);
            }
          }
        }
      }
    }

    for (int v = 0; v < n; v++) {
      if (indegrees[v] != 0)
        sb.append(v).append(" ");
    }

    System.out.println(sb);
  }
}	//	Main-class-end
```