# 26_01_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5624 좋은 수

[5624번: 좋은 수](http://boj.ma/5624/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st;

    int n = Integer.parseInt(br.readLine());
    int[] sequence = new int[n];
    Set<Integer> set = new HashSet<>();
    int answer = 0;

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      sequence[i] = Integer.parseInt(st.nextToken());

    for(int d = 0; d < n; d++) {
      boolean ok = false;
      for(int c = 0; c < d; c++) {
        if(set.contains(sequence[d] - sequence[c])) {
          ok = true;
          break;
        }
      }

      if(ok)
        answer++;

      for(int a = 0; a <= d; a++)
        set.add(sequence[a] + sequence[d]);
    }

    System.out.println(answer);
  }    //    main-end
}    //    Main-class-end
```