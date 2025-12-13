# 25_12_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 11812 K진 트리

[11812번: K진 트리](http://boj.ma/11812/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    StringTokenizer st = new StringTokenizer(br.readLine());

    long n = Long.parseLong(st.nextToken());
    int k = Integer.parseInt(st.nextToken());
    int q = Integer.parseInt(st.nextToken());

    for(int i = 0; i < q; i++) {
      st = new StringTokenizer(br.readLine());
      long a =  Long.parseLong(st.nextToken()) - 1;
      long b = Long.parseLong(st.nextToken()) - 1;

      sb.append(dist(a, b, k)).append("\n");
    }

    System.out.print(sb);
  } //  main-end

  private static long parent(long x, int k) {	//	x : 0 ~ N - 1
    if(x == 0)
      return 0;

    if(x % k == 0)
      return x / k - 1;

    return x / k;
  }

  private static long dist(long x, long y, int k) {
    if(k == 1)	//	skewed tree일 경우
      return Math.abs(y - x);

    long ret = 0;

    while(x != y) {	//	x와 y가 같지 않을 경우
      long xx = Math.max(x, y);	//	더 큰 정점(X)이 더 깊은 위치에 존재(적어도 같은 깊이)
      long yy = Math.min(x, y);

      x = parent(xx, k);	//	더 깊은 위치에 있는 정점을 부모 위치로 이동
      y = yy;
      ret++;
    }	//	둘이 같아질 때까지 이동

    return ret;
  }
} // Main-class-end
```