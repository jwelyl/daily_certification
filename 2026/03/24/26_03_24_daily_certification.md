# 26_03_24_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5859 Liars and Truth Tellers

[5859번: Liars and Truth Tellers](http://boj.ma/5859/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int m = Integer.parseInt(st.nextToken());

    //	1 ~ n : 진실 말하는 번호
    //	n + 1 ~ 2n : 거짓말하는 번호
    int[] parents = new int[2 * n + 1];
    for(int v = 1; v <= 2 * n; v++)
      parents[v] = v;

    for(int q = 1; q <= m; q++) {
      st = new StringTokenizer(br.readLine());
      int v1T = Integer.parseInt(st.nextToken());
      int v2T = Integer.parseInt(st.nextToken());
      char rel = st.nextToken().charAt(0);

      int v1F = v1T + n;
      int v2F = v2T + n;

      int p1T = find(v1T, parents);
      int p1F = find(v1F, parents);
      int p2T = find(v2T, parents);
      int p2F = find(v2F, parents);

      if (rel == 'T') {
        if (p1T == p2F || p1F == p2T) {
          System.out.println(q - 1);
          return;
        }
        union(p1T, p2T, parents);
        union(p1F, p2F, parents);
      } else {
        if (p1T == p2T || p1F == p2F) {
          System.out.println(q - 1);
          return;
        }
        union(p1T, p2F, parents);
        union(p1F, p2T, parents);
      }
    }

    System.out.println(m);
  } //	main-end

  private static int find(int v, int[] parents) {
    return v == parents[v] ? v : (parents[v] = find(parents[v], parents));
  }

  private static void union(int v1, int v2, int[] parents) {
    if(v1 != v2)
      parents[v2] = v1;
  }
} //	Main-class-end
```