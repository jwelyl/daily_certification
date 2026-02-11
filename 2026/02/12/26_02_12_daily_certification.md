# 26_02_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 7453 합이 0인 네 정수

[7453번: 합이 0인 네 정수](http://boj.ma/7453/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st;
    int n = Integer.parseInt(br.readLine());  // 배열 크기

    int[] arrA = new int[n];
    int[] arrB = new int[n];
    int[] arrC = new int[n];
    int[] arrD = new int[n];
    int[] arrAB = new int[n * n]; //  A[a] + B[b] 저장 배열
    int[] arrCD = new int[n * n]; //  C[c] + D[d] 저장 배열
    int idx = 0;

    int left = 0;
    int right = n * n - 1;

    long answer = 0L;  // A[a] + B[b] + C[c] + D[d] = 0 개수

    for (int i = 0; i < n; i++) {
      st = new StringTokenizer(br.readLine());

      arrA[i] = Integer.parseInt(st.nextToken());
      arrB[i] = Integer.parseInt(st.nextToken());
      arrC[i] = Integer.parseInt(st.nextToken());
      arrD[i] = Integer.parseInt(st.nextToken());
    }

    for (int a = 0; a < n; a++) {
      for (int b = 0; b < n; b++)
        arrAB[idx++] = arrA[a] + arrB[b];
    }

    idx = 0;
    for (int c = 0; c < n; c++) {
      for (int d = 0; d < n; d++)
        arrCD[idx++] = arrC[c] + arrD[d];
    }

    Arrays.sort(arrAB);
    Arrays.sort(arrCD);

    while(left < n * n && right >= 0) {
      long sum = (long)arrAB[left] + arrCD[right];

      if(sum == 0) {
        long sameLeftCount = 0;
        long sameRightCount = 0;

        int leftValue = arrAB[left];
        int rightValue = arrCD[right];

        //  arrAB에서 같은 값 개수
        while(left < n * n && arrAB[left] == leftValue) {
          sameLeftCount++;
          left++;
        }
        //  arrCD에서 같은 값 개수
        while(right >= 0 && arrCD[right] == rightValue) {
          sameRightCount++;
          right--;
        }

        answer += sameLeftCount * sameRightCount;
      }
      else if(sum < 0)
        left++;
      else
        right--;
    }

    System.out.println(answer);
  } //  main-end
} //  Main-class-end

```