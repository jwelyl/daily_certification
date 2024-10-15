# 24_10_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1025 제곱수 찾기

[](https://www.acmicpc.net/problem/1025)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int ans = -1;
	
	private static char[][] board;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		board = new char[N][M];
		
		for(int r = 0; r < N; r++)
			board[r] = br.readLine().toCharArray();
		
		for(int r = 0; r < N; r++) {
			for(int c = 0; c < M; c++) {
				int num = board[r][c] - '0';
				
				if(isSquare(num))
					ans = Math.max(ans, num);
				
				for(int rd = -8; rd <= 8; rd++) {
					for(int cd = -8; cd <= 8; cd++) {
						if(rd == 0 && cd == 0)
							continue;
						
						int cnum = num;
						
						for(int k = 1; ; k++) {
							int nr = r + rd * k;
							int nc = c + cd * k;
							
							if(!isIn(nr, nc))
								break;
							
							cnum = cnum * 10 + (board[nr][nc] - '0');
							
							if(isSquare(cnum))
								ans = Math.max(ans, cnum);
						}
					}
				}
			}
		}
		
		System.out.println(ans);
	}	//	main-end
	
	private static boolean isSquare(int num) {
		int sqrt = (int)Math.sqrt(num);
		
		return sqrt * sqrt == num;
	}
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < M);
	}
}	//	Main-class-end
```

### BOJ 3079 입국심사

[](https://www.acmicpc.net/problem/3079)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int[] times;
	
	private static int maxTime = 0;	//	심사가 가장 오래 걸리는 입국심사대 심사 시간
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		times = new int[N];
		
		for(int i = 0; i < N; i++) {
			times[i] = Integer.parseInt(br.readLine());
			maxTime = Math.max(maxTime, times[i]);
		}
	
		System.out.println(parametricSearch());
	}	//	main-end
	
	private static long parametricSearch() {
		long start = 0;
		long end = 1L * maxTime * M;	//	M명이 모두 가장 오래 걸리는 입국심사대에서 심사 받을 경우 걸리는 시간
		long res = end;
		
		while(start <= end) {
			long mid = (start + end) / 2;
			
			if(ok(mid)) {	//	mid 안에 M명 모두 심사 가능할 경우
				res = mid;	//	일단 mid 저장
				end = mid - 1;	//	시간 더 줄여보기
			}
			else start = mid + 1;	//	시간 더 늘려보기
		}
		
		return res;
	}
	
	//	mid 시간 동안 각 심사대가 몇명 심사할 수 있는지 계산
	//	합이 M 이상일 경우 mid 안에 심사 가능
	private static boolean ok(long mid) {
		long cnt = 0;
		
		for(int i = 0; i < N; i++) {
			cnt += mid / times[i];
			
			if(cnt >= M)
				return true;
		}
		
		return false;
	}
}	//	Main-class-end
```