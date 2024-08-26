# 24_08_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1749 점수따먹기

[](https://www.acmicpc.net/problem/1749)

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
	
	private static long[][] prefixSum;
	
	private static long maxSum = -10_000L * 40_000 - 1;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		prefixSum = new long[N + 1][M + 1];
		for(int r = 1; r <= N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 1; c <= M; c++)
				prefixSum[r][c] = prefixSum[r][c - 1] + prefixSum[r - 1][c] - prefixSum[r - 1][c - 1] + Integer.parseInt(st.nextToken());
		}
		
		for(int luy = 1; luy <= N; luy++) {
			for(int lux = 1; lux <= M; lux++) {
				for(int rdy = luy; rdy <= N; rdy++) {
					for(int rdx = lux; rdx <= M; rdx++)
						maxSum = Math.max(maxSum, partSum(luy, lux, rdy, rdx));
				}
			}
		}
		
		System.out.println(maxSum);
	}	//	main-end
	
	private static long partSum(int luy, int lux, int rdy, int rdx) {
		return prefixSum[rdy][rdx] - prefixSum[rdy][lux - 1] - prefixSum[luy - 1][rdx] + prefixSum[luy - 1][lux - 1];
	}
}	//	Main-class-end
```