# 26_02_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9007 카누 선수

[9007번: 카누 선수](http://boj.ma/9007/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
  private static final int NONE = 40_000_001;

  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();
    int t = Integer.parseInt(br.readLine());

    for (int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int k = Integer.parseInt(st.nextToken());   //  목표 무게
      int n = Integer.parseInt(st.nextToken());   //  4개 반 각각의 학생 수

      int[][] arr = new int[4][n];
      int[] arr12 = new int[n * n];
      int[] arr34 = new int[n * n];
      int idx = 0;

      int start = 0;
      int end = n * n - 1;

      int minDiff = NONE;
      int answer = NONE;

      for (int i = 0; i < 4; i++) {
        st = new StringTokenizer(br.readLine());
        for (int j = 0; j < n; j++)
          arr[i][j] = Integer.parseInt(st.nextToken());
      }

      for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
          arr12[idx] = arr[0][i] + arr[1][j];
          arr34[idx++] = arr[2][i] + arr[3][j];
        }
      }

      Arrays.sort(arr12);
      Arrays.sort(arr34);

     while(start < n * n && end >= 0) {
       int sum = arr12[start] + arr34[end];

       if(sum == k) {  //  4개 합이 딱 k일 경우
         answer = k;
         break;
       }
       else if(sum < k) { //  4개 합이 k보다 작을 경우
         int diff = k - sum;
         if(diff < minDiff) {
           minDiff = diff;
           answer = sum;
         }
         else if(diff == minDiff && sum < answer)
           answer = sum;
         start++; //  4개 합을 더 키워야 함
       }
       else { //  4개 합이 k보다 클 경우
         int diff = sum - k;
         if(diff < minDiff) {
           minDiff = diff;
           answer = sum;
         }
         else if(diff == minDiff && sum < answer)
           answer = sum;
         end--; //  4개 합을 더 줄여야 함
       }
     }

      sb.append(answer).append("\n");
    }

    System.out.print(sb);
  }   //  main-end
}   //  Main-class-end
```