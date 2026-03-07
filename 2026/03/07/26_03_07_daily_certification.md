# 26_03_07_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20943 카카오톡

[20943번: 카카오톡](http://boj.ma/20943/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws Exception {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    long res = 0;
    Map<Vector2D, Integer> map = new HashMap<>();

    for (int i = 0; i < n; i++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int a = Integer.parseInt(st.nextToken());
      int b = Integer.parseInt(st.nextToken());

      Vector2D vec = new Vector2D(a, b);
      map.put(vec, map.getOrDefault(vec, 0) + 1);
    }

    res = (long) n * (n - 1) / 2;

    for (int cnt : map.values())
      res -= (long) cnt * (cnt - 1) / 2;

    System.out.println(res);
  }   //  main-end

  private static int gcd(int num1, int num2) {
    return num2 == 0 ? num1 : gcd(num2, num1 % num2);
  }

  private static class Vector2D {
    public int x;
    public int y;

    public Vector2D(int x, int y) {
      int gcd = gcd(x, y);
      this.x = x / gcd;
      this.y = y / gcd;
    }

    @Override
    public boolean equals(Object obj) {
      if (this == obj)
        return true;
      if (!(obj instanceof Vector2D))
        return false;
      Vector2D other = (Vector2D) obj;
      return x == other.x && y == other.y;
    }

    @Override
    public int hashCode() {
      return Objects.hash(x, y);
    }
  }
}   //  Main-class-end
```