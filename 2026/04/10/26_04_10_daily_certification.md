# 26_04_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2450 모양 정돈

[2450번: 모양 정돈](http://boj.ma/2450/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.ArrayList;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

    int n = Integer.parseInt(br.readLine());

    int[] shapes = new int[n];
    int[] cnts = new int[4];

    int[][] orders = {
        {1, 2, 3},
        {1, 3, 2},
        {2, 1, 3},
        {2, 3, 1},
        {3, 1, 2},
        {3, 2, 1}
    };
    
    int answer = Integer.MAX_VALUE;

    StringTokenizer st = new StringTokenizer(br.readLine());
    for (int i = 0; i < n; i++) {
      shapes[i] = Integer.parseInt(st.nextToken());
      cnts[shapes[i]]++;
    }

    for (int i = 0; i < 6; i++) {
      answer = Math.min(answer, calculate(n, shapes, cnts, orders[i]));
    }

    System.out.println(answer);
  } //  main-end

  private static int calculate(int n, int[] shapes, int[] cnts, int[] order) {
    ArrayList<Integer> nums = new ArrayList<>();
    int[][] pos = new int[4][4];

    for (int i = 0; i < cnts[order[0]]; i++) nums.add(order[0]);
    for (int i = 0; i < cnts[order[1]]; i++) nums.add(order[1]);
    for (int i = 0; i < cnts[order[2]]; i++) nums.add(order[2]);

    for (int i = 0; i < n; i++)
      pos[nums.get(i)][shapes[i]]++;

    int answer = 0;

    for (int i = 1; i <= 3; i++) {
      for (int j = 1; j <= 3; j++) {
        if (i == j || pos[i][j] == 0) continue;

        if (pos[j][i] != 0) {
          int cnt = Math.min(pos[i][j], pos[j][i]);
          pos[i][j] -= cnt;
          pos[i][i] += cnt;
          pos[j][i] -= cnt;
          pos[j][j] += cnt;
          answer += cnt;
        }

        int next = 0;
        for (int k = 1; k <= 3; k++) {
          if (k == i || k == j) continue;
          next = k;
        }

        if (pos[next][i] != 0) {
          int cnt = Math.min(pos[i][j], pos[next][i]);
          pos[i][j] -= cnt;
          pos[i][i] += cnt;
          pos[next][i] -= cnt;
          pos[next][j] += cnt;
          answer += cnt;
        }
      }
    }

    return answer;
  }
} //  Main-class-end
```