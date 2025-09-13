# 25_09_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2302 극장 좌석

[2302번: 극장 좌석](http://boj.ma/2302/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
	private static int N;
	private static int M;
	private static int prev = 0;
	
	private static int[] dp;	//	dp[i] : i명을 앉히는 경우의 수 
	
	private static int answer = 1;
	
    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	
    	dp = new int[N + 1];
    	dp[0] = 1;
    	dp[1] = 1;
    	for(int i = 2; i <= N; i++)
    		dp[i] = dp[i - 1] + dp[i - 2];
 
    	M = Integer.parseInt(br.readLine());
    	for(int i = 0; i < M; i++) {
    		int vip = Integer.parseInt(br.readLine());
    		
    		answer *= dp[vip - prev - 1];
    		prev = vip;
    	}
    	
    	answer *= dp[N - prev];
    	
    	System.out.println(answer);
    }	//	main-end
}	//	Main-class-end
```

### BOJ 1344 축구

[1344번: 축구](http://boj.ma/1344/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public class Main {
    private static final int SEG = 18;	//	5분씩 90분 총 18개 구간
    private static final int[] PRIMES = {2, 3, 5, 7, 11, 13, 17};    //    가능한 소수 득점 수
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    
    private static double PA;    //    A 팀 득점 확률
    private static double PB;    //    B 팀 득점 확률
    
    //    dpA/B[i][j] : j개 구간 동안 A/B 팀이 득점한 점수가 i일 확률
    private static final double[][] dpA = new double[SEG + 1][SEG + 1];
    private static final double[][] dpB = new double[SEG + 1][SEG + 1];
    
    private static double PPA = 0.0;    //    A 팀이 소수만큼 득점했을 확률
    private static double PPB = 0.0;    //   B 팀이 소수만큼 득점했을 확률
    
    public static void main(String[] args) throws IOException {
        PA = Double.parseDouble(br.readLine()) / 100;
        PB = Double.parseDouble(br.readLine()) / 100;
        
        //    첫번쨰 구간에 A/B가 득점할 확률
        dpA[1][1] = PA;
        dpB[1][1] = PB;
        //    첫번쨰 구간에 A/B가 득점하지 못할 확률
        dpA[0][1] = 1 - PA;
        dpB[0][1] = 1 - PB;
        
        for(int j = 2; j <= SEG; j++) {
            //    j번째 구간까지도 0점일 확률 (j 구간 동안 모두 못 넣음)
            dpA[0][j] = dpA[0][j - 1] * (1 - PA);
            dpB[0][j] = dpB[0][j - 1] * (1 - PB);
            
            for(int i = 1; i < j; i++) {
                //    j - 1 구간까지 i - 1점 만듬 + 이번 구간에 골을 넣음
                dpA[i][j] = dpA[i - 1][j - 1] * PA;
                dpB[i][j] = dpB[i - 1][j - 1] * PB;
                
                //    j - 1 구간까지 i점 만듬 + 이번 구간에 골을 못넣음
                dpA[i][j] += dpA[i][j - 1] * (1 - PA);
                dpB[i][j] += dpB[i][j - 1] * (1 - PB);
            }
            
            //    j 구간까지 j점일 확률 (j 구간 동안 모두 넣음)
            dpA[j][j] = dpA[j - 1][j - 1] * PA;
            dpB[j][j] = dpB[j - 1][j - 1] * PB;
        }
        
        for(int prime : PRIMES) {
            PPA += dpA[prime][SEG];
            PPB += dpB[prime][SEG];
        }
        
        System.out.println(PPA + PPB - PPA * PPB);
    }    //    main-end
}    //    Main-class-end
```