# 25_06_10_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2629 양팔저울

[2629번: 양팔저울](http://boj.ma/2629/t)

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
	private static int[] weights;
	private static int allSum = 0;
	private static int M;	
	
	//	answer[nth][w] : 0 ~ nth 추만으로 w를 만들 수 있으면 true
	private static boolean[][] answer;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		weights = new int[N];
		
		st = new StringTokenizer(br.readLine());
		for(int w = 0; w < N; w++) {
			weights[w] = Integer.parseInt(st.nextToken());
			allSum += weights[w];
		}
		
		answer = new boolean[N][allSum + 1];
		
		dfs(0, 0, 0);
		
		M = Integer.parseInt(br.readLine());
		st = new StringTokenizer(br.readLine());
		for(int q = 0; q < M; q++) {
			int w = Integer.parseInt(st.nextToken());			
			
			if(w > allSum)
				sb.append("N ");
			else {
				boolean ok = false;
				for(int i = 0; i < N; i++) {
					if(answer[i][w]) {
						ok = true;
						break;
					}
				}
				
				sb.append(ok ? "Y " : "N ");
			}
		}
		
		System.out.println(sb);
	}	//	main-end
	
	private static void dfs(int nth, int leftSum, int rightSum) {
		if(nth == N)	//	N개 추에 대해 모두 확인
			return;
	
		//	nth번째 추를 사용하지 않음
		int diff = Math.abs(leftSum - rightSum);
		if(!answer[nth][diff]) {
			answer[nth][diff] = true;
			dfs(nth + 1, leftSum, rightSum);
		}
		//	nth번째 추를 왼쪽에 놓음
		diff = Math.abs(leftSum + weights[nth] - rightSum);
		if(!answer[nth][diff]) {
			answer[nth][diff] = true;
			dfs(nth + 1, leftSum + weights[nth], rightSum);
		}
		//	nth번째 추를 오른쪽에 놓음
		diff = Math.abs(rightSum + weights[nth] - leftSum);
		if(!answer[nth][diff]) {
			answer[nth][diff] = true;
			dfs(nth + 1, leftSum, rightSum + weights[nth]);
		}
	}
}	//	Main-class-end
```