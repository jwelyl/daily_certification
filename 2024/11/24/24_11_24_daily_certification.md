# 24_11_24_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 24450  **国土分割 (Land Division)**

[24450번: 国土分割 (Land Division)](https://boj.ma/24450/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int H;
	private static int W;
	
	private static int[][] prefixSum;
	
	private static final Set<Integer> divisors = new HashSet<>();
	private static final List<List<int[]>> colDivisors = new ArrayList<>();
	private static final List<List<int[]>> rowDivisors = new ArrayList<>();
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		H = Integer.parseInt(st.nextToken());
		W = Integer.parseInt(st.nextToken());
	
		prefixSum = new int[H + 1][W + 1];
		for(int h = 1; h <= H; h++) {
			st = new StringTokenizer(br.readLine());
			for(int w = 1; w <= W; w++)
				prefixSum[h][w] = prefixSum[h - 1][w] + prefixSum[h][w - 1] - prefixSum[h - 1][w - 1] + Integer.parseInt(st.nextToken());
		}
		
		for(int num = 2; num * num <= prefixSum[H][W]; num++) {
			if(prefixSum[H][W] % num == 0) {
				divisors.add(num);
				divisors.add(prefixSum[H][W] / num);
			}
		}
		
		divide(true);	//	세로방향 분할 체크
		divide(false);	//	가로방향 분할 체크

		combination();
		
		System.out.println(answer);
	}	//	main-end
	
	private static void combination() {
		for(List<int[]> colSegments : colDivisors) {
			for(List<int[]> rowSegments : rowDivisors) {
				int prevPartSum = -1;
				boolean ok = true;
				
				OUTER:
				for(int[] colSegment : colSegments) {
					int col1 = colSegment[0];
					int col2 = colSegment[1];
					
					for(int[] rowSegment : rowSegments) {
						int row1 = rowSegment[0];
						int row2 = rowSegment[1];
						
						int partSum = partialSum(row1, col1, row2, col2);
						
						if(prevPartSum == -1)
							prevPartSum = partSum;
						else if(prevPartSum != partSum) {
							ok = false;
							break OUTER;
						}
					}
				}
				
				if(ok)
					answer++;
			}
		}
	}
	
	private static void divide(boolean col) {
		for(int divisor : divisors) {	//	세로/가로 방향으로 divisor로 분할 가능한지 체크
			List<int[]> segments = new ArrayList<>();
			
			int start = 1;
			int end = start;
			boolean ok = true;
			
			while(true) {
				int partSum = col ? partialSum(1, start, H, end) : partialSum(start, 1, end, W);
				
				if(partSum > divisor) {	//	divisor로 균등하게 분할할 수 없을 경우
					ok = false;
					break;
				}
				else if(partSum == divisor) {
					segments.add(new int[] {start, end});
					
					if(end == (col ? W : H))
						break;
					
					start = end + 1;
					end = start;
				}
				else
					end++;
			}
			
			if(ok) {	//	세로/가로 방향으로 divisor로 분할 가능할 경우
				if(col)
					colDivisors.add(segments);
				else
					rowDivisors.add(segments);
				answer++;
			}
		}
	}
	
	private static int partialSum(int y1, int x1, int y2, int x2) {
		return prefixSum[y2][x2] - prefixSum[y2][x1 - 1] - prefixSum[y1 - 1][x2] + prefixSum[y1 - 1][x1 - 1];
	}
}	//	Main-class-end
```