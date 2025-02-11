# 25_02_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 4781 사탕 가게

[4781번: 사탕 가게](http://boj.ma/4781/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static int[] calories;
    private static int[] costs;
    private static int[] dp;	//	dp[i] : 1~i번째 사탕을 이용해 가격 M 이내로 구매했을때 최대 칼로리
    
    public static void main(String[] args) throws IOException {
        while(true) {
        	st = new StringTokenizer(br.readLine());
        	N = Integer.parseInt(st.nextToken());
        	M = (int)(Double.parseDouble(st.nextToken()) * 100 + 0.5);
        
        	if(N == 0 && M == 0) {
        		System.out.print(sb);
        		break;
        	}
        	
        	calories = new int[N + 1];
        	costs = new int[N + 1];
        	
        	dp = new int[M + 1];
        	
        	for(int i = 1; i <= N; i++) {
        		st = new StringTokenizer(br.readLine());
            	calories[i] = Integer.parseInt(st.nextToken());
            	costs[i] = (int)(Double.parseDouble(st.nextToken()) * 100 + 0.5);
        	}
        	
        	for(int i = 1; i <= N; i++) {
        		int calorie = calories[i];		//	i번째 사탕 칼로리
        		int cost = costs[i];			//	i번째 사탕 가격
        	
        		for(int j = cost; j <= M; j++)
        			dp[j] = Math.max(dp[j], dp[j - cost] + calorie); 
        	}
        	
        	sb.append(dp[M]).append("\n");
        }
    }    //    main-end
}    //    Main-class-end
```

### BOJ 2073 수도배관공사

[2073번: 수도배관공사](http://boj.ma/2073/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int D;    //    목표 길이
    private static int P;    //    수도관 종류
    
    private static int[] lengths;    	//    lengths[i] : i 종류의 파이프 길이
    private static int[] capacities;    //    capacities[i] : i 종류의 수도관 용량
    
    private static int[] dp;	//	dp[l] : 길이 l인 수도관을 만들때 만들어진 수도관의 최대 용량
   
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        D = Integer.parseInt(st.nextToken());
        P = Integer.parseInt(st.nextToken());
        
        lengths = new int[P + 1];
        capacities = new int[P + 1];
        
        for(int p = 1; p <= P; p++) {
        	st = new StringTokenizer(br.readLine());
            lengths[p] = Integer.parseInt(st.nextToken());
            capacities[p] = Integer.parseInt(st.nextToken());
        }

        dp = new int[D + 1];
        dp[0] = Integer.MAX_VALUE;
        
        for(int p = 1; p <= P; p++) {	//	1 ~ p 파이프만으로 수도관을 만듬 
        	for(int d = D; d >= lengths[p]; d--)
        		dp[d] = Math.max(dp[d], Math.min(dp[d - lengths[p]], capacities[p]));
        }
        
        System.out.println(dp[D]);
    }    //    main-end
}    //    Main-class-end
```