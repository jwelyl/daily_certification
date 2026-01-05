# 26_01_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 31501 DP (Small)

[31501번: DP (Small)](http://boj.ma/31501/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringBuilder sb = new StringBuilder();
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());    //    문제 개수
        int q = Integer.parseInt(st.nextToken());    //    쿼리 개수
        
        //    difficulties[i] : i번째 문제 난이도
        int[] difficulties = new int[n];
        //    lisEnd[i] : i번째 문제를 끝으로 하는 문제 난이도 순 LIS 길이
        int[] lisEnd = new int[n];
        //    lisStart[i] : i번쩨 문제를 시작으로 하는 문제 난이도 순 LIS 길이
        int[] lisStart = new int[n];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < n; i++) {
            difficulties[i] = Integer.parseInt(st.nextToken());
            lisEnd[i] = 1;
            lisStart[i] = 1;
        }
        
        for(int i = 1; i < n; i++) {
            for(int j = i - 1; j >= 0; j--) {
                if(difficulties[i] > difficulties[j])
                    lisEnd[i] = Math.max(lisEnd[i], lisEnd[j] + 1);
            }
        }
        
        for(int i = n - 2; i >= 0; i--) {
            for(int j = i + 1; j < n; j++) {
                if(difficulties[i] < difficulties[j])
                    lisStart[i] = Math.max(lisStart[i], lisStart[j] + 1);
            }
        }
        
        for(int i = 0; i < q; i++) {
            int num = Integer.parseInt(br.readLine()) - 1;
            sb.append(lisEnd[num] + lisStart[num] - 1).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 29728 실버와 소수는 둘다 S로 시작한다

[29728번: 실버와 소수는 둘다 S로 시작한다](http://boj.ma/29728/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;

public class Main {
  private static final int MAX = 5_000_000;

  public static void main(String[] args) throws IOException {
    boolean[] sieve = init(); //  sieve[num] = false일 경우, num은 prime

    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int n = Integer.parseInt(br.readLine());

    boolean reverse = false;  //  true일 경우, 가장 앞에 넣고 빼기, false일 경우 뒤에 넣고 빼기
    Deque<Character> deque = new ArrayDeque<>();

    int bCnt = 0;
    int sCnt = 0;

    for (int turn = 1; turn <= n; turn++) {
      if (!sieve[turn]) {  //  소수번째 턴일 경우
        if (reverse) {
          char ch = deque.peekFirst();

          if (ch == 'B') {
            deque.pollFirst();
            bCnt--;
            deque.offerFirst('S');
            deque.offerFirst('S');
            sCnt += 2;
          } else {
            deque.offerFirst('S');
            sCnt++;
          }
        } else {
          char ch = deque.peekLast();

          if (ch == 'B') {
            deque.pollLast();
            bCnt--;
            deque.offerLast('S');
            deque.offerLast('S');
            sCnt += 2;
          } else {
            deque.offerLast('S');
            sCnt++;
          }
        }

        reverse = !reverse; //  뒤집기
      } else {
        if (reverse) {
          deque.offerFirst('B');
        } else {
          deque.offerLast('B');
        }
        bCnt++;
      }
    }

    System.out.println(bCnt + " " + sCnt);
  } // main-end

  private static boolean[] init() {
    boolean[] sieve = new boolean[MAX + 1];

    sieve[1] = true;

    for (int num = 2; num * num <= MAX; num++) {
      if (!sieve[num]) {
        for (int mult = num * num; mult <= MAX; mult += num) {
          sieve[mult] = true;
        }
      }
    }

    return sieve;
  }
} // Main-class-end
```