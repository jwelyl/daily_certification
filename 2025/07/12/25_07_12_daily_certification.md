# 25_07_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2045 마방진

[2045번: 마방진](http://boj.ma/2045/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 20_000;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static final int[][] ms = new int[3][3];

	public static void main(String[] args) throws IOException {
		for (int y = 0; y < 3; y++) {
			st = new StringTokenizer(br.readLine());
			for (int x = 0; x < 3; x++)
				ms[y][x] = Integer.parseInt(st.nextToken());
		}

		//	msSum : 마방진의 행, 열, 대각선 합
		for (int msSum = 3; msSum <= MAX * 3; msSum++) {
			int[][] temp = new int[3][3];
			for (int y = 0; y < 3; y++)
				System.arraycopy(ms[y], 0, temp[y], 0, 3);

			// 반복 횟수 제한: 3번만 전파 시도
			for (int k = 0; k < 3; k++) {
				for (int y = 0; y < 3; y++) {
					for (int x = 0; x < 3; x++) {
						if (temp[y][x] != 0) 
							continue;

						// 행
						if (temp[y][(x + 1) % 3] != 0 && temp[y][(x + 2) % 3] != 0) {
							int val = msSum - temp[y][(x + 1) % 3] - temp[y][(x + 2) % 3];
							if (isValid(val)) 
								temp[y][x] = val;
						}

						// 열
						if (temp[(y + 1) % 3][x] != 0 && temp[(y + 2) % 3][x] != 0) {
							int val = msSum - temp[(y + 1) % 3][x] - temp[(y + 2) % 3][x];
							if (isValid(val)) 
								temp[y][x] = val;
						}
					}
				}
			}

			if (isMagicSquare(msSum, temp)) {
				for (int y = 0; y < 3; y++)
					System.arraycopy(temp[y], 0, ms[y], 0, 3);
				break;
			}
		}

		for (int y = 0; y < 3; y++) {
			for (int x = 0; x < 3; x++)
				sb.append(ms[y][x]).append(" ");
			sb.append("\n");
		}
		System.out.print(sb);
	}	//	main-end

	private static boolean isValid(int val) {
		return 1 <= val && val <= MAX;
	}

	private static boolean isMagicSquare(int msSum, int[][] mat) {
		for (int i = 0; i < 3; i++) {
			//	i행의 합이 msSum인지 체크
			if (mat[i][0] + mat[i][1] + mat[i][2] != msSum) 
				return false;
			//	i열의 합이 msSum인지 체크
			if (mat[0][i] + mat[1][i] + mat[2][i] != msSum) 
				return false;
		}
		
		//	우하향 대각선 합 msSum인지 체크
		if (mat[0][0] + mat[1][1] + mat[2][2] != msSum) 
			return false;
		//	우상향 대각선 합 msSum인지 체크
		if (mat[0][2] + mat[1][1] + mat[2][0] != msSum) 
			return false;

		// 값 범위 체크
		for (int y = 0; y < 3; y++)
			for (int x = 0; x < 3; x++)
				if (!isValid(mat[y][x])) return false;

		return true;
	}
}	//	Main-class-end
```