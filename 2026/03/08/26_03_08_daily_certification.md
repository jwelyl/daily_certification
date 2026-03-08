# 26_03_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 28137 뭐라고? 안들려

[28137번: 뭐라고? 안들려](http://boj.ma/28137/t)

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
    StringTokenizer st = new StringTokenizer(br.readLine());

    int n = Integer.parseInt(st.nextToken()); //  좌표평면 위 점의 개수
    int k = Integer.parseInt(st.nextToken()); //  요구하는 기울기

    //  Key : y 절편 b, Value : 기울기가 k이고 y 절편이 b인 직선에 속하는 점의 개수
    Map<Long, Integer> map = new HashMap<>();

    long answer = 0;  //  두 점을 잇는 직선의 기울기가 k인 두 점을 고르는 경우의 수 (순서 유관)

    for(int i = 0; i < n; i++) {
      st = new StringTokenizer(br.readLine());
      int x = Integer.parseInt(st.nextToken());
      int y = Integer.parseInt(st.nextToken());
      long b = y - (long)k * x; //  기울기가 k인 직선이 (x, y)를 지날때 그 직선의 y 절편
      
      map.put(b, map.getOrDefault(b, 0) + 1);
    }

    for(int cnt : map.values())
      answer += (long)cnt * (cnt - 1);  //  같은 직선에서 두 점을 순서 유관하게 고르는 경우의 수

    System.out.println(answer);
  } //	main-end
} //	Main-class-end
```