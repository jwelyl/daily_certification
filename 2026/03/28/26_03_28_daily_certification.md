# 26_03_28_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 16288 Passport Control**

[16288번: Passport Control](http://boj.ma/16288/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
  public static void main(String[] args) throws IOException {
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    StringTokenizer st = new StringTokenizer(br.readLine());
    int n = Integer.parseInt(st.nextToken());   //  입국 승객 수
    int k = Integer.parseInt(st.nextToken());   //  여권 심사 창구의 수
    int[] order = new int[n];
    Deque<Integer>[] stacks = new ArrayDeque[k];

    st = new StringTokenizer(br.readLine());
    for(int i = 0; i < n; i++)
      order[i] = Integer.parseInt(st.nextToken());
    for(int i = 0; i < k; i++) {
      stacks[i] = new ArrayDeque<>();
      stacks[i].offerLast(n + 1);
    }

    for(int i = n - 1; i >= 0; i--) {
      int cur = order[i];

      boolean find = false;
      for(int j = 0; j < k; j++) {
        if(stacks[j].peekLast() > cur) {
          stacks[j].offerLast(cur);
          find = true;
          break;
        }
      }

      if(!find) {
        System.out.println("NO");
        return;
      }
    }

    System.out.println("YES");
  } //	main-end
} //	Main-class-end
```