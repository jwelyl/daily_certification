# 26_01_31_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2900 프로그램

[2900번: 프로그램](http://boj.ma/2900/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken());
    int k = Integer.parseInt(st.nextToken());
    int q;

    int[] arr = new int[n];
    long[] prefixSum = new long[n];

    Map<Integer, Integer> map = new HashMap<>();

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < k; i++) {
      int x = Integer.parseInt(st.nextToken());
      map.put(x, map.getOrDefault(x, 0) + 1);
    }

    for(Map.Entry<Integer, Integer> entry : map.entrySet()) {
      int x = entry.getKey();
      int cnt = entry.getValue();

      int quotient = n / x;
      int remain = n % x;

      for(int i = 0; i < quotient; i++) {
        arr[i * x] += cnt;
        if(i * x + 1 < n)
          arr[i * x + 1] -= cnt;
      }

      if(remain != 0) {
        arr[quotient * x] += cnt;
        if(quotient * x + 1 < n)
          arr[quotient * x + 1] -= cnt;
      }
    }

    for(int i = 0; i < n; i++) {
      if(i == 0)
        prefixSum[i] = arr[i];
      else {
        arr[i] = arr[i - 1] + arr[i];
        prefixSum[i] = prefixSum[i - 1] + arr[i];
      }
    }

    q = Integer.parseInt(br.readLine());
    for(int i = 0; i < q; i++) {
      st = new StringTokenizer(br.readLine());

      int l = Integer.parseInt(st.nextToken());
      int r = Integer.parseInt(st.nextToken());

      long ps = l == 0 ? prefixSum[r] : prefixSum[r] - prefixSum[l - 1];

      sb.append(ps).append("\n");
    }

    System.out.print(sb);
  } //	main-end
} //	Main-class-end
```