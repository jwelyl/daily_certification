# 26_04_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1300 K번째 수

[1300번: K번째 수](http://boj.ma/1300/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    long n = Long.parseLong(br.readLine());
    long k = Long.parseLong(br.readLine());

    System.out.println(parametricSearch(n, k));
  } //  main-end

  private static long parametricSearch(long n, long k) {
    long start = 1L;
    long end = n * n;
    long ret = n * n;

    while (start <= end) {
      long mid = (start + end) / 2;

      long cnt = cnts(mid, n); // mid 이하 수의 개수

      if (cnt >= k) {       // mid 이하 수의 개수가 k개 이상이면
        ret = mid;        // 일단 mid 저장
        end = mid - 1;    // 더 작게 탐색
      }
      else                // mid 이하 수의 개수가 k보다 적을 경우
        start = mid + 1;  // 더 크게 탐색
    }

    return ret;
  }

  // num 이하 수의 개수
  private static long cnts(long num, long n) {
    long res = 0L;

    for (long i = 1L; i <= n; i++) {
      long q = num / i;

      if (q == 0L)
        break;

      res += Math.min(n, q);
    }

    return res;
  }
} //  Main-class-end
```