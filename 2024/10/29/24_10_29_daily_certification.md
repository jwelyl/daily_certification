# 24_10_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13904 과제

[](https://www.acmicpc.net/problem/13904)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;
import java.util.PriorityQueue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    
    private static final List<int[]> works = new ArrayList<>();
    private static final PriorityQueue<Integer> pq = new PriorityQueue<>();

    private static int ans = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int n = 0; n < N; n++) {
            st = new StringTokenizer(br.readLine());
            int deadline = Integer.parseInt(st.nextToken());
            int reward = Integer.parseInt(st.nextToken());
            
            works.add(new int[] {deadline, reward});
        }
        
        //    데드라인이 짧은 순서대로 정렬
        Collections.sort(works, (w1, w2) -> Integer.compare(w1[0], w2[0]));
        
        for(int[] work : works) {
            int deadline = work[0];
            int reward = work[1];
            
            pq.offer(reward);    //   해당 과제를 한다고 가정
            if(pq.size() > deadline)    //    모든 과제를 할 수 없을 경우
                pq.poll();    //   가장 보상이 적은 과제 포기
        }
        
        while(!pq.isEmpty())
            ans += pq.poll();
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 3933 **라그랑주의 네 제곱수 정리**

[](https://www.acmicpc.net/problem/3933)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int MAX = 1 << 15;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int N;
    
    private static final int[][] dp = new int[5][MAX];
    
    public static void main(String[] args) throws IOException {
    	dp();
    	
        while(true) {
        	N = Integer.parseInt(br.readLine());
        	
        	if(N == 0) {
        		System.out.print(sb);
        		return;
        	}
        	
        	sb.append(dp[1][N] + dp[2][N] + dp[3][N] + dp[4][N]).append("\n");
        }
    }    //    main-end
    
    private static void dp() {
    	for(int num1 = 1; num1 * num1 < MAX; num1++) {
    		dp[1][num1 * num1] = 1;	//	1개의 제곱수로 만들 수 있는 수는 어차피 제곱수뿐임
    		
    		for(int num2 = num1 * num1; num2 < MAX; num2++) {
    			dp[2][num2] += dp[1][num2 - num1 * num1];
    			dp[3][num2] += dp[2][num2 - num1 * num1];
    			dp[4][num2] += dp[3][num2 - num1 * num1];
    		}
    	}
    }
}    //    Main-class-end
```