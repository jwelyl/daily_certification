# 26_02_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23059 리그 오브 레게노

[23059번: 리그 오브 레게노](http://boj.ma/23059/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int n = Integer.parseInt(br.readLine());

    List<String[]> orders = new ArrayList<>();
    Set<String> itemSet = new HashSet<>();
    List<String> itemList = new ArrayList<>();

    // 입력 처리
    for (int i = 0; i < n; i++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      String prev = st.nextToken();
      String next = st.nextToken();

      orders.add(new String[] {prev, next});

      if (!itemSet.contains(prev)) {
        itemSet.add(prev);
        itemList.add(prev);
      }
      if (!itemSet.contains(next)) {
        itemSet.add(next);
        itemList.add(next);
      }
    }

    Collections.sort(itemList);

    Map<String, Integer> itemNumMap = new HashMap<>();
    for (int i = 0; i < itemList.size(); i++)
      itemNumMap.put(itemList.get(i), i + 1);

    List<Integer>[] graph = new ArrayList[itemSet.size() + 1];
    for (int i = 0; i <= itemList.size(); i++)
      graph[i] = new ArrayList<>();

    int[] indegrees = new int[itemList.size() + 1];

    // 그래프 구성
    for (int v = 0; v < n; v++) {
      int prev = itemNumMap.get(orders.get(v)[0]);
      int next = itemNumMap.get(orders.get(v)[1]);

      indegrees[next]++;
      graph[prev].add(next);
    }

    List<String> result = topologicalSort(graph, indegrees, itemList, itemList.size());

    if (result.size() < itemList.size())
      System.out.println(-1);
    else {
      for (String item : result)
        sb.append(item).append("\n");
      System.out.print(sb);
    }
  } //  main-end

  private static List<String> topologicalSort(List<Integer>[] graph, int[] indegrees, List<String> itemList, int nItems) {
    Queue<Integer> queue = new ArrayDeque<>();
    List<String> res = new ArrayList<>();

    for (int i = 1; i <= nItems; i++) {
      if (indegrees[i] == 0) {
        queue.offer(i);
        res.add(itemList.get(i - 1));
      }
    }

    while (!queue.isEmpty()) {
      int size = queue.size();
      PriorityQueue<Integer> nextList = new PriorityQueue<>();

      for (int i = 0; i < size; i++) {
        int cur = queue.poll();

        for (int next : graph[cur]) {
          indegrees[next]--;
          if (indegrees[next] == 0)
            nextList.offer(next);
        }
      }

      while (!nextList.isEmpty()) {
        int next = nextList.poll();
        queue.offer(next);
        res.add(itemList.get(next - 1));
      }
    }

    return res;
  }
} //  Main-class-end
```