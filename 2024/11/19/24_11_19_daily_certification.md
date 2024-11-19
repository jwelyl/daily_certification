# 24_11_19_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 1687** 행렬 찾기

[1687번: 행렬 찾기](https://boj.ma/1687/t)

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
		
		for(int r = 1; r <= N; r++) {
			String input = br.readLine();
			for(int c = 1; c <= M; c++)
				prefixSum[r][c] = prefixSum[r - 1][c] + prefixSum[r][c- 1] - prefixSum[r - 1][c - 1] + (input.charAt(c - 1) - '0');
		}
		
		for(int c1 = 1; c1 <= M; c1++) {
			for(int c2 = c1; c2 <= M; c2++) {
				int area = 0;	//	(r1, c1)을 좌상단, (r2, c2)를 우하단으로 하는 0으로만 이루어진 부분 직사각형 0의 개수
				
				for(int r = 1; r <= N; r++) {
					//	r행에서 [c1, c2]에 해당하는 부분의 0의 개수
					int lineArea = prefixSum[r][c2] - prefixSum[r][c1 - 1] - prefixSum[r - 1][c2] + prefixSum[r - 1][c1 - 1];
				
					if(lineArea == 0) {	//	해당 부분에 1이 없을 경우
						area += c2 - c1 + 1;	//	해당 부분의 0의 개수 추가
						answer = Math.max(answer, area);
					}
					else	//	1이 있을 경우, 기존의 0으로만 이루어진 부분 직사각형은 사라짐
						area = 0;
				}
			}
		}
		
		System.out.println(answer);
	}	//	main-end
}	//	Main-class-end
```