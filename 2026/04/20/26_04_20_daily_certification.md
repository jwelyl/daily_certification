# 26_04_20_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9007 카누 선수

[](http://boj.ma/9007/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for(int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int target = Integer.parseInt(st.nextToken());
      int n = Integer.parseInt(st.nextToken());

      int before = Integer.MAX_VALUE;
      int minDiff = Integer.MAX_VALUE;
      int answer = 0;

      int[][] arr = new int[4][n];
      int[][] sumArr = new int[2][n * n];
      for(int nth = 0; nth < 4; nth++) {
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
          arr[nth][i] = Integer.parseInt(st.nextToken());
      }

      for(int nth = 0; nth < 4; nth += 2) {
        int idx = 0;

        for(int i = 0; i < n; i++) {
          for(int j = 0; j < n; j++)
            sumArr[nth / 2][idx++] = arr[nth][i] + arr[nth + 1][j];
        }
      }

      Arrays.sort(sumArr[1]);

      for(int sum1 : sumArr[0]) {
        if(sum1 == before)
          continue;

        //  sum1 + sum2가 target에 가장 가까운 값을 찾아야 함
        //  가장 가까운 값이 큰 값, 작은 값이 있을 경우 작은 값을 취함
        int sum2 = target - sum1; //  찾아야 할 값
        int idx = lowerBound(sumArr[1], sum2);
        int partAnswer = 0;

        if(idx == -1) //  sum1 + sum2가 target보다 작거나 같은 sum2는 존재하지 않음. (sum1이 이미 너무 큼)
          partAnswer = sum1 + sumArr[1][0]; //  sum1 + sum2의 최솟값
        else {
          if(idx < n * n - 1) {
            int partAnswer1 = sum1 + sumArr[1][idx];
            int partAnswer2 = sum1 + sumArr[1][idx + 1];
            int diff1 = Math.abs(target - partAnswer1);
            int diff2 = Math.abs(target - partAnswer2);

            if(diff2 < diff1)
              partAnswer = partAnswer2;
            else
              partAnswer = partAnswer1;
          }
          else
            partAnswer = sum1 + sumArr[1][n * n - 1];
        }

        if(Math.abs(target - partAnswer) < minDiff) {
          answer = partAnswer;
          minDiff = Math.abs(target - partAnswer);
        }
        else if(Math.abs(target - partAnswer) == minDiff) {
          if(partAnswer < answer)
            answer = partAnswer;
        }

        before = sum1;
      }

      sb.append(answer).append("\n");
    }

    System.out.print(sb);
  } //  main-end

  //  sumArr에서 target보다 작거나 같으면서 가장 큰 값 찾기
  private static int lowerBound(int[] sumArr, int target) {
    int start = 0;
    int end = sumArr.length - 1;
    int res = -1;

    while(start <= end) {
      int mid = (start + end) / 2;

      if(sumArr[mid] == target)
        return mid;

      if(sumArr[mid] > target)
        end = mid - 1;
      else {
        res = mid;
        start = mid + 1;
      }
    }

    return res;
  }
} //  Main-class-end
```

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringBuilder sb = new StringBuilder();

    int t = Integer.parseInt(br.readLine());

    for(int tc = 1; tc <= t; tc++) {
      StringTokenizer st = new StringTokenizer(br.readLine());
      int target = Integer.parseInt(st.nextToken());
      int n = Integer.parseInt(st.nextToken());

      int start = 0;
      int end = n * n - 1;
      int minDiff = Integer.MAX_VALUE;
      int answer = 0;

      int[][] arr = new int[4][n];
      int[][] squareArr = new int[2][n * n];
      for(int nth = 0; nth < 4; nth++) {
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++)
          arr[nth][i] = Integer.parseInt(st.nextToken());
      }

      for(int nth = 0; nth < 4; nth += 2) {
        int idx = 0;

        for(int i = 0; i < n; i++) {
          for(int j = 0; j < n; j++)
            squareArr[nth / 2][idx++] = arr[nth][i] + arr[nth + 1][j];
        }

        Arrays.sort(squareArr[nth / 2]);
      }

      while(start < n * n && end >= 0) {
        int sum = squareArr[0][start] + squareArr[1][end];

        int diff = sum - target;
        int absDiff = Math.abs(diff);

        if(diff == 0) {
          answer = target;
          break;
        }

        if(absDiff < minDiff) {
          minDiff = absDiff;
          answer = sum;
        }
        else if(absDiff == minDiff) {
          if(sum < answer)
            answer = sum;
        }

        if(diff > 0)
          end--;
        else
          start++;
      }

      sb.append(answer).append("\n");
    }

    System.out.print(sb);
  } //  main-end
} //  Main-class-end
```