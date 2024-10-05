# 24_10_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14698 **전생했더니 슬라임 연구자였던 건에 대하여 (Hard)**

[](https://www.acmicpc.net/problem/14698)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.PriorityQueue;

public class Main {
    private static final int MOD = 1_000_000_007;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int T;
    private static int N;
    private static final PriorityQueue<Long> pq = new PriorityQueue<>();
    
    public static void main(String[] args) throws IOException {
        T = Integer.parseInt(br.readLine());
        
        for(int tc = 0; tc < T; tc++) {
            pq.clear();
            
            long ans = 1;
            N = Integer.parseInt(br.readLine());
            
            st = new StringTokenizer(br.readLine());
            for(int i = 0; i < N; i++)
                pq.offer(Long.parseLong(st.nextToken()));
                        
            while(pq.size() >= 2) {
                long e1 = pq.poll();
                long e2 = pq.poll();
                long e = e1 * e2;
                
                ans = ((ans % MOD) * (e % MOD)) % MOD;
                
                pq.offer(e);
            }
            
            sb.append(ans).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

### BOJ 1484 다이어트

[](https://www.acmicpc.net/problem/1484)

**Mathematics**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.List;
import java.util.ArrayList;
import java.util.Collections;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static final List<Integer> nums = new ArrayList<>();
    
    private static int G;
    
    public static void main(String[] args) throws IOException {
        G = Integer.parseInt(br.readLine());
        
        for(int d = 1; d <= (int)Math.sqrt(G); d++) {
            if(G % d != 0)
                continue;
            
            int yMinusX = d;
            int yPlusX = G / d;
            
            if(yMinusX == yPlusX || (yMinusX + yPlusX) % 2 != 0)
                continue;
            
            nums.add((yMinusX + yPlusX) / 2);
        }
        
        if(nums.isEmpty())
        	sb.append(-1).append("\n");
        else {
	        Collections.sort(nums);
	        for(int num : nums)
	            sb.append(num).append("\n");
        }
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```

**Two-Pointer**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final int MAX = 50_001;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    
    private static int G;
    
    private static int cnt = 0;    //    정답 개수
    
    public static void main(String[] args) throws IOException {
        G = Integer.parseInt(br.readLine());
        
        int start = 1;    //    이전 몸무게
        int end = 2;      //    현재 몸무게
        
        while(end <= MAX) {
            int diff = end * end - start * start;    //    현재 몸무게^2 - 이전 몸무게^2
            
            if(diff == G) {    //    제곱의 차가 G일 경우, 정답 케이스에 포함
                sb.append(end).append("\n");
                cnt++;
                start++;
                end++;
            }
            else if(diff < G)    //    제곱의 차가 G보다 작을 경우, end를 키워서 차이 키우기
                end++;
            else    //    제곱의 차가 G보다 클 경우, start를 키워서 차이 줄이기
                start++;
        }
        
        if(cnt == 0)
            sb.append("-1\n");
        
        System.out.print(sb);
    }    //    main-end
}    //    Main-class-end
```