# 25_12_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1998 이미지 압축

[1998번: 이미지 압축](http://boj.ma/1998/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int m = Integer.parseInt(st.nextToken()); //  줄 수 있는 사탕 수
    int n = Integer.parseInt(st.nextToken());  //  사탕 받고 싶어하는 친구 수

    int[] candies = new int[n];
    long sum = 0;   //  친구들이 원하는 사탕 수 합
    long lack = 0;  //  부족한 사탕 수
    long answer = 0;

    for(int i = 0; i < n; i++) {
      candies[i] = Integer.parseInt(br.readLine());
      sum += candies[i];
    }

    lack = sum - m;

    Arrays.sort(candies);
    for(int candy : candies) {  //  현재 친구가 원하는 사탕 수 candy
      long cnt = Math.min(candy, lack / n);   //  해당 친구가 받지 못할 사탕 수 (최대한 균등하게 부족하게 하기)
      n--;
      answer += cnt * cnt;
      lack -= cnt;
    }

    System.out.println(answer);
  } //	main-end
} //	Main-class-end
```