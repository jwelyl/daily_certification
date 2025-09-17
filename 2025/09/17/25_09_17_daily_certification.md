# 25_09_17_daily_certification

# Problem Solving (Algorithm & SQL)

### SW Expert Academy 4193. 수영대회 결승전

[SW Expert Academy](https://swexpertacademy.com/main/code/userProblem/userProblemDetail.do?contestProbId=AWKaG6_6AGQDFARV)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class Solution {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int T;
	private static int N;
	
	private static int[][] map;
	
	private static int sy;
	private static int sx;
	private static int ey;
	private static int ex;
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			N = Integer.parseInt(br.readLine());
			
			map = new int[N][N];
			for(int y = 0; y < N; y++) {
				st = new StringTokenizer(br.readLine());
				
				for(int x = 0; x < N; x++)
					map[y][x] = Integer.parseInt(st.nextToken());
			}
			
			st = new StringTokenizer(br.readLine());
			sy = Integer.parseInt(st.nextToken());
			sx = Integer.parseInt(st.nextToken());
			st = new StringTokenizer(br.readLine());
			ey = Integer.parseInt(st.nextToken());
			ex = Integer.parseInt(st.nextToken());
			
			sb.append("#").append(tc).append(" ").append(bfs()).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static int bfs() {
		Queue<int[]> queue = new LinkedList<>();
		boolean[][] visited = new boolean[N][N];
		
		visited[sy][sx] = true;
		queue.offer(new int[] {sy, sx, 0});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cy = cur[0];
			int cx = cur[1];
			int cc = cur[2];
			
			for(int d = 0; d < 4; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				//	범위 안이고, 장애물이 아니며, 아직 방문하지 않았을 경우
				if(isIn(ny, nx) && map[ny][nx] != 1 && !visited[ny][nx]) {
					if(ny == ey && nx == ex)	//	도착점일 경우
						return cc + 1;
					
					if(map[ny][nx] == 2) {	//	소용돌이일 경우
						if(cc % 3 == 2) {	//	소용돌이가 비활성화된 경우
							visited[ny][nx] = true;
							queue.offer(new int[] {ny, nx, cc + 1});	//	소용돌이로 이동
						}
						else	//	소용돌이가 활성 상태인 경우
							queue.offer(new int[] {cy, cx, cc + 1});	//	현재 위치에서 대기
					}
					else {	//	빈칸일 경우
						visited[ny][nx] = true;
						queue.offer(new int[] {ny, nx, cc + 1});
					}
				}
			}
			
		}
		
		return -1;	//	도착할 수 없음
	}
	
	private static final int[] dy = {0, 1, 0, -1};
	private static final int[] dx = {1, 0, -1, 0};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y < N) && (0 <= x && x < N);
	}
}	//	Solution-class-end
```

### BOJ 1943 동전 분배

[1943번: 동전 분배](http://boj.ma/1943/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	동전 종류
	private static int[][] coins;	//	coins[nth] : nth번째 동전의 {가치, 개수}
	private static int[] dp;		//	dp[value] : value를 만들 수 있는 경우의 수
	
	private static int sum = 0;	//	모든 동전 가치 합
	
	public static void main(String[] args) throws IOException {
		for(int tc = 1; tc <= 3; tc++) {
			N = Integer.parseInt(br.readLine());
			coins = new int[N + 1][2];
			sum = 0;
			
			for(int nth = 1; nth <= N; nth++) {
				st = new StringTokenizer(br.readLine());
				coins[nth][0] = Integer.parseInt(st.nextToken());
				coins[nth][1] = Integer.parseInt(st.nextToken());
				
				sum += coins[nth][0] * coins[nth][1];
			}
			
			if(sum % 2 == 1) {	//	동전 가치 합이 홀수라 절반으로 나눌 수 없을 경우
				sb.append(0).append("\n");
				continue;
			}
			
			dp = new int[sum / 2 + 1];
			Arrays.fill(dp, NONE);
			dp[0] = 0;
			
			for(int nth = 1; nth <= N; nth++) {
				int value = coins[nth][0];
				int cnt = coins[nth][1];
				
				for(int prevValue = 0; prevValue <= sum / 2; prevValue++) {
					if(dp[prevValue] == NONE)
						continue;
					
					if(prevValue + value <= sum / 2 && dp[prevValue] < cnt)
						dp[prevValue + value] = Math.min(dp[prevValue + value], dp[prevValue] + 1);
					
					dp[prevValue] = 0;
				}
			}
			
			sb.append(dp[sum / 2] == NONE ? 0 : 1).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
} //	Main-class-end
```