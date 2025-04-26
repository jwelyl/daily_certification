# 25_04_26_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 3050 집들이

[3050번: 집들이](http://boj.ma/3050/t)

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
	
	private static int[][] prefixSum;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		prefixSum = new int[N + 1][M + 1];
		for(int y = 1; y <= N; y++) {
			String input = br.readLine();
			for(int x = 1; x <= M; x++) {
				char ch = input.charAt(x - 1);
				
				prefixSum[y][x] = prefixSum[y - 1][x] + prefixSum[y][x - 1] - prefixSum[y - 1][x - 1];
				if(ch == '.')
					prefixSum[y][x] += 1;
			}
		}
		
		for(int y1 = 1; y1 <= N; y1++) {
			for(int y2 = y1; y2 <= N; y2++) {
				int x1 = 1;
				int x2 = x1;
				
				while(x1 <= M && x2 <= M) {
					if(partialSum(y1, x1, y2, x2) == (y2 - y1 + 1) * (x2 - x1 + 1)) {
						answer = Math.max(answer, 2 * (y2 - y1 + 1) + 2 * (x2 - x1 + 1) - 1);
						x2++;
					}
					else {
						x1 = x2 + 1;
						x2 = x1;
					}
				}
			}
		}
		
		System.out.println(answer);
	} //	main-end
	
	private static int partialSum(int y1, int x1, int y2, int x2) {
		return prefixSum[y2][x2] - prefixSum[y2][x1 - 1] - prefixSum[y1 - 1][x2] + prefixSum[y1 - 1][x1 - 1];
	}
} //	Main-class-end
```