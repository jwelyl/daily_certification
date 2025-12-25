# 25_12_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13147 Dwarves

[13147번: Dwarves](http://boj.ma/13147/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());
    Map<String, Integer> map = new HashMap<>();
    int num = 0;

    List<Integer>[] graph = new ArrayList[2 * n + 1];
    int[] indegrees = new int[2 * n + 1];
    for(int v = 0; v <= 2 * n; v++)
      graph[v] = new ArrayList<>();

    for(int i = 0; i < n; i++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      String name1 = st.nextToken();
      char comp = st.nextToken().charAt(0);
      String name2 = st.nextToken();

      int num1 = map.getOrDefault(name1, -1);
      int num2 = map.getOrDefault(name2, -1);

      if(num1 == -1) {
        map.put(name1, ++num);
        num1 = num;
      }
      if(num2 == -1) {
        map.put(name2, ++num);
        num2 = num;
      }

      if(comp == '>') {
        graph[num1].add(num2);
        indegrees[num2]++;
      }
      else {
        graph[num2].add(num1);
        indegrees[num1]++;
      }
    }

    topologicalSort(graph, indegrees, num);
  } //  main-end

  private static void topologicalSort(List<Integer>[] graph, int[] indegrees, int cnt) {
    Queue<Integer> queue = new ArrayDeque<>();

    int sorted = 0;
    for(int v = 1; v <= cnt; v++) {
      if(indegrees[v] == 0) {
        queue.offer(v);
        sorted++;
      }
    }

    while(!queue.isEmpty()) {
      int cur = queue.poll();

      for(int next : graph[cur]) {
        indegrees[next]--;
        if(indegrees[next] == 0) {
          queue.offer(next);
          sorted++;
        }
      }
    }

    System.out.println(sorted == cnt ? "possible" : "impossible");
  }
} //  Main-class-end
```