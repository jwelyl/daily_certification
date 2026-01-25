# 26_01_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4568 LRU Caching

[http://boj.ma/4568/t](http://boj.ma/4568/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedHashMap;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    for (int tc = 1; ; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int size = Integer.parseInt(st.nextToken());
      char[] sequence;

      LinkedHashMap<Character, Integer> map = new LinkedHashMap<>(size, 1.0f, true);

      if (size == 0) {
        System.out.print(sb);
        break;
      }

      sb.append("Simulation ").append(tc).append("\n");
      sequence = st.nextToken().toCharArray();

      for (int time = 0; time < sequence.length; time++) {
        char data = sequence[time];

        if (data == '!') { //  캐시 내용 출력
          for (char ch : map.keySet())
            sb.append(ch);
          sb.append("\n");
        } else {  //  데이터 추가
          //  캐시 잔여 공간이 존재하거나, 이미 존재하는 데이터를 추가하는 경우
          if (map.size() < size || map.containsKey(data))
            map.put(data, time);
          else {
            //  가장 오래 전에 접근한 데이터 제거
            char eldest = map.keySet().iterator().next();
            map.remove(eldest);
            map.put(data, time);
          }
        }
      }
    }
  } //	main-end
} //	Main-class-end
```