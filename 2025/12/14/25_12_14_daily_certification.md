# 25_12_14_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2087 암호문

[2087번: 암호문](http://boj.ma/2087/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());  // 비트 수
    int[] aArr = new int[n];
    int mid = (n - 1) / 2;
    long k;

    //  오른쪽 n / 2 비트로 만들 수 있는 합 집합
    Set<Long> rightSet = new HashSet<>();

    StringBuilder answer = new StringBuilder();

    for(int i = 0; i < n; i++)
      aArr[i] = Integer.parseInt(br.readLine());

    k = Long.parseLong(br.readLine());

    //  오른쪽 n / 2 비트로 만들 수 있는 합 만들기
    for(long mask = 0; mask < (1L << (n - mid - 1)); mask++) {
      long sum = 0;

      for(int i = mid + 1; i < n; i++) {
        if((mask & (1L << (n - 1 - i))) != 0)
          sum += aArr[i];
      }

      rightSet.add(sum);
    }

    //  왼쪽 n / 2 비트로 만들 수 있는 합
    for(long mask = 0; mask < (1L << (mid + 1)); mask++) {
      long sum = 0;
      StringBuilder sb = new StringBuilder();

      for(int i = 0; i <= mid; i++) {
        if((mask & (1L << (mid - i))) != 0) {
          sum += aArr[i];
          sb.append("1");
        }
        else
          sb.append("0");
      }

      if(rightSet.contains(k - sum)) {
        answer.append(sb);
        k = k - sum;  //  찾아야 할 오른쪽 부분을 k로 설정
        break;
      }
    }

    //  오른쪽 n / 2 비트로 만들 수 있는 합 중에 k에 해당하는 비트 문자열 찾기
    for(long mask = 0; mask < (1L << (n - mid - 1)); mask++) {
      long sum = 0;
      StringBuilder sb = new StringBuilder();

      for(int i = mid + 1; i < n; i++) {
        if((mask & (1L << (n - 1 - i))) != 0) {
          sum += aArr[i];
          sb.append("1");
        }
        else
          sb.append("0");
      }

      if(sum == k) {
        answer.append(sb);
        break;
      }
    }

    System.out.println(answer);
  } //  main-end
} // Main-class-end
```