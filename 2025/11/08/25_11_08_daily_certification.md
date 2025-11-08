# 25_11_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6988 타일 밟기

[6988번: 타일 밟기](http://boj.ma/6988/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_NUM = 1_000_000;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;            //    타일 개수
    private static int[] tiles;      //    타일 목록 (숫자 오름차순)
    
    private static final boolean[] SET = new boolean[MAX_NUM * 2 + 1];
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        tiles = new int[N + 1];
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++) {
            tiles[i] = Integer.parseInt(st.nextToken());
            SET[tiles[i]] = true;
        }        
        
        for(int i = 1; i < N; i++) {
        	for(int j = i + 1; j <= N; j++) {
        		int diff = tiles[j] - tiles[i];
        		int len = 2;
        		int cur = tiles[j];
        		
        		long sum = tiles[i] + tiles[j];
        		
        		while(SET[cur + diff]) {
        			cur += diff;
        			sum += cur;
        			len++;
        		}
        		
        		if(len >= 3)
        			answer = Math.max(answer, sum);
        	}
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;            //    타일 개수
    private static int[] tiles;      //    타일 목록 (숫자 오름차순)
    
    //	dp[i] : 
    private static Map<Integer, Long>[] dp;
    
    private static long answer = 0;
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        tiles = new int[N + 1];
        dp = new HashMap[N + 1];
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++) {
            tiles[i] = Integer.parseInt(st.nextToken());
            dp[i] = new HashMap<>();
        }        
        
        for(int i = 2; i <= N; i++) {
        	for(int j = i - 1; j >= 1; j--) {
        		int diff = tiles[i] - tiles[j];
        		
        		if(dp[j].get(diff) == null)		//	j번째 수에서 끝나는 공차가 diff인 등차수열이 없을 경우
        			dp[i].put(diff, (long)tiles[j] + tiles[i]);	//	길이 2인 등차수열 생성
        		else {	//	j번째 수에서 끝나는 공차가 diff인 등차수열이 이미 존재할 경우
        			long sum = dp[j].get(diff) + tiles[i];	//	i번째 수까지 합하여 길이 3 이상인 등차수열 합 생성
        			dp[i].put(diff, sum);	
        			answer = Math.max(answer, sum);
        		}
        	}
        }
        
        System.out.println(answer);
    }    //    main-end
}    //    Main-class-end
```