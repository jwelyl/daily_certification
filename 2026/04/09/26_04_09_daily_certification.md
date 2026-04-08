# 26_04_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4195 친구 네트워크

[4195번: 친구 네트워크](http://boj.ma/4195/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX = 200_001; // 친구 관계 수 최대 100,000 → 사람 수 최대 200,000

  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    int[] parents = new int[MAX];  // parents[i] : i의 대표 노드
    int[] sizeArr = new int[MAX];    // sizeArr[i]   : 대표 i가 이끄는 집합의 크기

    for (int tc = 0; tc < t; tc++) {
      HashMap<String, Integer> map = new HashMap<>();

      // 초기화
      for (int v = 0; v < MAX; v++) {
        parents[v] = v;
        sizeArr[v] = 1;
      }

      int f = Integer.parseInt(br.readLine());
      int num = 0; // 새로 등장하는 친구에게 줄 번호

      for (int i = 0; i < f; i++) {
        StringTokenizer st = new StringTokenizer(br.readLine());
        String f1 = st.nextToken();
        String f2 = st.nextToken();

        if (!map.containsKey(f1))
          map.put(f1, num++);
        if (!map.containsKey(f2))
          map.put(f2, num++);

        int n1 = map.get(f1);
        int n2 = map.get(f2);

        int networkSize = union(n1, n2, parents, sizeArr);
        sb.append(networkSize).append('\n');
      }
    }

    System.out.print(sb);
  } //  main-end

  private static int find(int x, int[] parents, int[] sizeArr) {
    return parents[x] == x ? x : (parents[x] = find(parents[x], parents, sizeArr));
  }

  private static int union(int a, int b, int[] parents, int[] sizeArr) {
    int rootA = find(a, parents, sizeArr);
    int rootB = find(b, parents, sizeArr);

    if (rootA != rootB) {
      // 한쪽(rootA)에 다른 쪽(rootB)를 붙임
      parents[rootB] = rootA;
      sizeArr[rootA] += sizeArr[rootB];
    }
    return sizeArr[find(rootA, parents, sizeArr)];
  }
} //  Main-class-end
```