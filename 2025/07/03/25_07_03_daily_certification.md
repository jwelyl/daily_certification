# 25_07_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 5904 Moo 게임

[5904번: Moo 게임](http://boj.ma/5904/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final int MAX = 27;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	
    private static int N;
    
    private static final int[] lArr = new int[MAX + 1];
    
    public static void main(String[] args) throws IOException {
    	N = Integer.parseInt(br.readLine());
    	
    	lArr[0] = 3;
    	for(int i = 1; i <= MAX; i++) {
    		lArr[i] = 2 * lArr[i - 1] + i + 3;
    		
    		if(lArr[i] >= N)
    			break;
    	}
    	
    	find(N);
    }	//	main-end
    
    //	nth번째 문자 찾기
    private static void find(int nth) {
    	int k = 0;	//	nth번째 문자는 S(k) 안에 포함됨
    	
    	for(; lArr[k] < nth; k++);
    	
    	if(k == 0) {
    		System.out.println(nth == 1 ? 'm' : 'o');
    		System.exit(0);
    	}
    	
    	//	prefix S(k - 1)과 suffix S(k - 1) 사이 moo...oo에 포함될 경우
    	if(lArr[k - 1] + 1 <= nth && nth <= lArr[k - 1] + (k + 3)) {
    		System.out.println(nth == lArr[k - 1] + 1 ? 'm' : 'o');
    		System.exit(0);
    	}
    	else {
    		int nnth = nth - lArr[k - 1] - (k + 3);	//	prefix S(k - 1)과 moo...oo를 제외했을때 찾으려는 위치
    		find(nnth);
    	}
    }
}	//	Main-class-end
```

### BOJ 2411 아이템 먹기

[2411번: 아이템 먹기](http://boj.ma/2411/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Map;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
    private static int N;
    private static int M;
    private static int A;
    private static int B;
    
    private static final Map<Integer, List<Integer>> yPosListMap = new TreeMap<>();

    private static boolean[][] obstacles;
    
    private static int cy = 0;
    private static int cx = 0;
    
    private static int answer = 1;
    
    public static void main(String[] args) throws IOException {
    	st = new StringTokenizer(br.readLine());
    	N = Integer.parseInt(st.nextToken());
    	M = Integer.parseInt(st.nextToken());
    	A = Integer.parseInt(st.nextToken());
    	B = Integer.parseInt(st.nextToken());
    	
    	obstacles = new boolean[N][M];
    	
    	for(int i = 0; i < A; i++) {
    		st = new StringTokenizer(br.readLine());
        	int y = Integer.parseInt(st.nextToken()) - 1;
        	int x = Integer.parseInt(st.nextToken()) - 1;
        	
        	List<Integer> yPosList = yPosListMap.getOrDefault(x, new ArrayList<>());
        	yPosList.add(y);
        	
        	yPosListMap.put(x, yPosList);
    	}
    	
    	for(int i = 0; i < B; i++) {
    		st = new StringTokenizer(br.readLine());
        	int y = Integer.parseInt(st.nextToken()) - 1;
        	int x = Integer.parseInt(st.nextToken()) - 1;
        	
        	obstacles[y][x] = true;
    	}
    	
    	for(List<Integer> yPosList : yPosListMap.values())
    		Collections.sort(yPosList);
    	
    	for(int x : yPosListMap.keySet()) {
    		List<Integer> yPosList = yPosListMap.get(x);
    		
    		int lowest = yPosList.get(0);
    		int highest = yPosList.get(yPosList.size() - 1);
    		
    		answer *= count(cy, cx, lowest, x);
    		cy = highest;
    		cx = x;
    	}
    	
    	answer *= count(cy, cx, N - 1, M - 1);
    	
    	System.out.println(answer);
    }	//	main-end
    
    //	(fy, fx)에서 (ty, tx)까지 가는 방법의 수
    private static int count(int fy, int fx, int ty, int tx) {
    	int[][] dp = new int[N][M];
    	
    	for(int y = fy; y <= ty; y++) {
    		if(obstacles[y][fx])
    			break;
    		
    		dp[y][fx] = 1;
    	}
    	
    	for(int x = fx; x <= tx; x++) {
    		if(obstacles[fy][x])
    			break;
    		
    		dp[fy][x] = 1;
    	}
    	
    	for(int y = fy + 1; y <= ty; y++) {
    		for(int x = fx + 1; x <= tx; x++) {
    			if(obstacles[y][x])
    				continue;
    			
    			dp[y][x] += dp[y - 1][x];	//	아래에서 위로 가기
    			dp[y][x] += dp[y][x - 1];	//	왼쪽에서 오른쪽으로 가기
    		}
    	}
    	
    	return dp[ty][tx];
    }
}	//	Main-class-end
```