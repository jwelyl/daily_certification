# 26_01_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 34060 로봇 청소기

[34060번: 로봇 청소기](http://boj.ma/34060/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
  private static final int MAX = 100_000;

  private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

  private static final long[] DIFF = new long[MAX + 1];    //    차분 배열 겸 누적합 배열

  public static void main(String[] args) throws IOException {
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());    //    참가자 수
    int t = Integer.parseInt(st.nextToken());    //    스터디 진행 시간 t

    for(int i = 0; i < n; i++) {
      int k = Integer.parseInt(br.readLine());    //    i번째 스터디원의 가능한 스터디 시간

      for(int j = 0; j < k; j++) {
        st = new StringTokenizer(br.readLine());
        int start = Integer.parseInt(st.nextToken());
        int end = Integer.parseInt(st.nextToken());

        DIFF[start]++;
        DIFF[end]--;
      }
    }

    int aStart = 0;
    int aEnd = 0;
    long answer = 0;

    //    차분 배열
    for(int i = 1; i <= MAX; i++)
      DIFF[i] = DIFF[i - 1] + DIFF[i];
    //    누적합 구하기
    for(int i = 1; i <= MAX; i++)
      DIFF[i] = DIFF[i - 1] + DIFF[i];

    for(int start = 0; start + t - 1 <= MAX; start++) {
      int end = start + t - 1;
      long partSum = start == 0 ? DIFF[end] : DIFF[end] - DIFF[start - 1];

      if(partSum > answer) {
        answer = partSum;
        aStart = start;
        aEnd = end + 1;
      }
    }

    System.out.println(aStart + " " + aEnd);
  }    //    main-end
}    //    Main-class-end
```