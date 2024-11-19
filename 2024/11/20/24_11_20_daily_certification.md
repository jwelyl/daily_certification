# 24_11_20_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 2829 아름다운 행렬

[2829번: 아름다운 행렬](https://boj.ma/2829/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;

	private static int[][] matrix;
	private static int[][] diagonalSumA;
	private static int[][] diagonalSumB;
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine().trim());
		
		matrix = new int[N + 1][N + 2];
		diagonalSumA = new int[N + 1][N + 2];
		diagonalSumB = new int[N + 1][N + 2];
		
		for(int r = 1; r <= N; r++) {
			st = new StringTokenizer(br.readLine());
			for(int c = 1; c <= N; c++)
				matrix[r][c] = Integer.parseInt(st.nextToken());
		}
		
		//	주 대각선(우하향 대각선) 누적합 구하기
		int startR = 1;
		int startC = 1;
		
		for(; startR <= N; startR++) {
			int curR = startR;
			int curC = startC;
			
			while(curR <= N && curC <= N) {
				diagonalSumA[curR][curC] = diagonalSumA[curR - 1][curC - 1] + matrix[curR][curC];
				curR++;
				curC++;
			}
		}
		
		startR = 1;
		startC = 2;
		
		for(; startC <= N; startC++) {
			int curR = startR;
			int curC = startC;
			
			while(curR <= N && curC <= N) {
				diagonalSumA[curR][curC] = diagonalSumA[curR - 1][curC - 1] + matrix[curR][curC];
				curR++;
				curC++;
			}
		}
		
		//	좌하향 대각선 누적합 구하기
		startR = 1;
		startC = N;
		
		for(; startR <= N; startR++) {
			int curR = startR;
			int curC = startC;
			
			while(curR <= N && curC >= 1) {
				diagonalSumB[curR][curC] = diagonalSumB[curR - 1][curC + 1] + matrix[curR][curC];
				curR++;
				curC--;
			}
		}
		
		startR = 1;
		startC = N - 1;
		
		for(; startC >= 1; startC--) {
			int curR = startR;
			int curC = startC;
			
			while(curR <= N && curC >= 1) {
				diagonalSumB[curR][curC] = diagonalSumB[curR - 1][curC + 1] + matrix[curR][curC];
				curR++;
				curC--;
			}
		}

		for(int r1 = 1; r1 <= N; r1++) {
			for(int c1 = 1; c1 <= N; c1++) {
				int r2 = r1;
				int c2 = c1;
				
				while(r2 <= N && c2 <= N) {
                    int sumA = partialSum(r1, c1, r2, c2, true);
					int sumB = partialSum(r1, c2, r2, c1, false);
					answer = Math.max(answer, sumA - sumB);
					
					r2++;
					c2++;
				}
			}
		}
		
		System.out.println(answer);
	}	//	main-end
	
	private static int partialSum(int r1, int c1, int r2, int c2, boolean diagonalA) {
		if(diagonalA)
			return diagonalSumA[r2][c2] - diagonalSumA[r1 - 1][c1 - 1];
		
		return diagonalSumB[r2][c2] - diagonalSumB[r1 - 1][c1 + 1];
	}
}	//	Main-class-end
```