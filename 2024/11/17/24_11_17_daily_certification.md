# 24_11_17_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 1184** 귀농

[1184번: 귀농](https://boj.ma/1184/t)

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

	private static int N;
	private static int[][] prefixSum;
	
	private static long answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		prefixSum = new int[N + 1][N + 1];
		
		for(int y = 1; y <= N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 1; x <= N; x++)
				prefixSum[y][x] = prefixSum[y - 1][x] + prefixSum[y][x - 1] - prefixSum[y - 1][x - 1] + Integer.parseInt(st.nextToken());
		}
		
		for(int y = 1; y < N; y++) {
			for(int x = 1; x < N; x++) {
				Map<Integer, Integer> luMap = new HashMap<>();
				Map<Integer, Integer> rdMap = new HashMap<>();
				
				for(int luy = 1; luy <= y; luy++) {
					for(int lux = 1; lux <= x; lux++) {
						int sum = partialSum(luy, lux, y, x);
						
						luMap.put(sum, luMap.getOrDefault(sum, 0) + 1);
					}
				}
				
				for(int rdy = y + 1; rdy <= N; rdy++) {
					for(int rdx = x + 1; rdx <= N; rdx++) {
						int sum = partialSum(y + 1, x + 1, rdy, rdx);
						
						rdMap.put(sum, rdMap.getOrDefault(sum, 0) + 1);
					}
				}
				
				for(Map.Entry<Integer, Integer> entry : luMap.entrySet()) {
					int luArea = entry.getKey();
					int luCnt = entry.getValue();
					int rdCnt = rdMap.getOrDefault(luArea, 0);
					
					answer += luCnt * rdCnt;
				}
				
				Map<Integer, Integer> ldMap = new HashMap<>();
				Map<Integer, Integer> ruMap = new HashMap<>();
				
				for(int ldy = y + 1; ldy <= N; ldy++) {
					for(int ldx = 1; ldx <= x; ldx++) {
						int sum = partialSum(y + 1, ldx, ldy, x);
						
						ldMap.put(sum, ldMap.getOrDefault(sum, 0) + 1);
					}
				}
				
				for(int ruy = 1; ruy <= y; ruy++) {
					for(int rux = x + 1; rux <= N; rux++) {
						int sum = partialSum(ruy, x + 1, y, rux);
						
						ruMap.put(sum, ruMap.getOrDefault(sum, 0) + 1);
					}
				}
				
				for(Map.Entry<Integer, Integer> entry : ldMap.entrySet()) {
					int ldArea = entry.getKey();
					int ldCnt = entry.getValue();
					int ruCnt = ruMap.getOrDefault(ldArea, 0);
					
					answer += ldCnt * ruCnt;
				}
			}
		}
		
		System.out.println(answer);
	}	//	main-end
	
	private static int partialSum(int y1, int x1, int y2, int x2) {
		return prefixSum[y2][x2] - prefixSum[y1 - 1][x2] - prefixSum[y2][x1 - 1] + prefixSum[y1 - 1][x1 - 1];
	}
}	//	Main-class-end
```