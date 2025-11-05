# 25_11_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 14938 서강그라운드

[14938번: 서강그라운드](http://boj.ma/14938/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 1_000_000;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int R;
	
	private static int[] items;

	private static int[][] graph;
	private static int maxItems = -1;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		R = Integer.parseInt(st.nextToken());
	
		items = new int[N + 1];
		graph = new int[N + 1][N + 1];
		for(int i = 0; i <= N; i++) {
			Arrays.fill(graph[i], INF);
			graph[i][i] = 0;
		}
		
		
		st = new StringTokenizer(br.readLine());
		for(int i = 1; i <= N; i++)
			items[i] = Integer.parseInt(st.nextToken());
		
		for(int i = 0; i < R; i++) {
			int a, b;
			
			st = new StringTokenizer(br.readLine());
			
			a = Integer.parseInt(st.nextToken());
			b = Integer.parseInt(st.nextToken());
			
			graph[a][b] = graph[b][a] = Integer.parseInt(st.nextToken());
		}

		floydWarshall();
		
		for(int start = 1; start <= N; start++) {
			int sum = 0;
			
			for(int end = 1; end <= N; end++) {
				if(graph[start][end] <= M)
					sum += items[end];
			}
			
			if(sum > maxItems)
				maxItems = sum;
		}
		
		System.out.println(maxItems);
	}	//	main-end
	
	private static void floydWarshall() {
		for(int k = 1; k <= N; k++) {
			for(int i = 1; i <= N; i++) {
				for(int j = 1; j <= N; j++) {
					graph[i][j] = Math.min(graph[i][j], graph[i][k] + graph[k][j]);
				}
			}
		}
	}
}	//	Main-class-end
```