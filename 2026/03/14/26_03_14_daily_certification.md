# 26_03_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1039 교환

[1039번: 교환](http://boj.ma/1039/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.ArrayDeque;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;
import java.util.Arrays;

public class Main {
  private static final int MAX = 1_000_000;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());
    int k = Integer.parseInt(st.nextToken());

    char[] input = Integer.toString(n).toCharArray();
    if(n < 10)    //    한 자릿수는 문제 조건에 맞게 변경할 수 없음
      System.out.println(-1);
    else
      bfs(input, k);
  }    //    main-end

  private static void bfs(char[] input, int k) {
    Queue<char[]> queue = new ArrayDeque<>();
    boolean[][] visited = new boolean[11][MAX + 1];
    int time = 0;    //    바꾼 횟수
    int answer = -1;

    visited[time][strToNum(input)] = true;
    queue.offer(input);

    while(!queue.isEmpty()) {
      int size = queue.size();

      for(int i = 0; i < size; i++) {
        char[] cinput = queue.poll();

        for(int idx1 = 0; idx1 < cinput.length - 1; idx1++) {
          for(int idx2 = idx1 + 1; idx2 < cinput.length; idx2++) {
            if(idx1 == 0 && cinput[idx2] == '0')
              continue;

            char[] ninput = Arrays.copyOf(cinput, cinput.length);
            char tmp = ninput[idx1];
            ninput[idx1] = ninput[idx2];
            ninput[idx2] = tmp;

            int nnum = strToNum(ninput);

            if(!visited[time + 1][nnum]) {
              visited[time + 1][nnum] = true;

              if(time == k - 1) {    //    이번 변경이 K번째 변경이 되는 경우
                answer = Math.max(answer, nnum);
                continue;
              }

              queue.offer(ninput);
            }
          }
        }
      }

      time++;
    }

    System.out.println(answer);
  }

  private static int strToNum(char[] str) {
    int res = 0;
    int mult = 1;

    for(int i = str.length - 1; i >= 0; i--) {
      res += (str[i] - '0') * mult;
      mult *= 10;
    }

    return res;
  }
}    //    Main-class-end
```