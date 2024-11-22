# 24_11_22_daily_certification

```
[#326 koreii] 데일리인증 20241122
1. 2025 상반기 취업 계획
- 2024 하반기 취준 과정에서 부족했던 점 간략하게 정리
- 2025 상반기 과업 간략하게 설정
2. 코딩 테스트 대비 알고리즘 학습
- Implementation, Prefix Sum, Bruteforcing (4092 삼각형의 값)
```

# Problem Solving (Algorithm & SQL)

### **BOJ** 4902  삼각형의 값

[4902번: 삼각형의 값](https://boj.ma/4902/t)

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
	private static int[][] prefixSum;	//	prefixSum[r][c] : r행의 1열부터 c열까지 값의 누적합
	
	private static int maxPartialSum;
	
	public static void main(String[] args) throws IOException {
		for(int tc = 1; ; tc++) {
			st = new StringTokenizer(br.readLine());
			
			N = Integer.parseInt(st.nextToken());
			
			if(N == 0) {
				System.out.print(sb);
				break;
			}
			
			maxPartialSum = Integer.MIN_VALUE;
			prefixSum = new int[N + 1][2 * N];
			
			for(int r = 1; r <= N; r++) {
				for(int c = 1; c <= 2 * r - 1; c++)
					prefixSum[r][c] = prefixSum[r][c - 1] + Integer.parseInt(st.nextToken());
			}
			
			//	(r, c)를 꼭짓점으로 정삼각형
			for(int r = 1; r <= N; r++){
				for(int c = 1; c <= 2 * r - 1; c += 2) {
					int sum = 0;
					
					for(int offset = 0; offset <= N - r; offset++) {
						int nr = r + offset;
						int from = c;
						int to = from + 2 * offset;
						
						if(!isIn(nr, from) || !isIn(nr, to))
							break;

						sum += partialSum(nr, from, to);
						maxPartialSum = Math.max(maxPartialSum, sum);
					}
				}
			}
			
			//	(r, c)를 꼭짓점으로 역삼각형
			for(int r = N; r >= 1; r--){
				for(int c = 2; c <= 2 * r - 1; c += 2) {
					int sum = 0;
					
					for(int offset = 0; offset <= r - 1; offset++) {
						int nr = r - offset;
						int to = c;
						int from = to - 2 * offset;
						
						if(!isIn(nr, from) || !isIn(nr, to))
							break;

						sum += partialSum(nr, from, to);
						maxPartialSum = Math.max(maxPartialSum, sum);
					}
				}
			}
			
			sb.append(tc).append(". ").append(maxPartialSum).append("\n");
		}
	}	//	main-end
	
	private static boolean isIn(int row, int x) {
		return 1 <= x && x <= 2 * row - 1;
	}
	
	private static int partialSum(int row, int from, int to) {
		return prefixSum[row][to] - prefixSum[row][from - 1];
	}
}	//	Main-class-end
```