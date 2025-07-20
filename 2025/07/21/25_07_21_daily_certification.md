# 25_07_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2026 소풍

[2026번: 소풍](http://boj.ma/2026/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int K;	//	클리크의 정점 수
	private static int N;	//	정점 수
	private static int F;	//	간선 수
	
	//	degrees[v] : v와 연결된 정점 개수
	private static int[] degrees;
	//	graph[u][v] : u와 v가 연결되어 있으면 true (graph[u][v] == graph[v][u])
	private static boolean[][] graph;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		K = Integer.parseInt(st.nextToken());
		N = Integer.parseInt(st.nextToken());
		F = Integer.parseInt(st.nextToken());
		
		degrees = new int[N + 1];
		graph = new boolean[N + 1][N + 1];
		
		for(int e = 0; e < F; e++) {
			st = new StringTokenizer(br.readLine());
			int u = Integer.parseInt(st.nextToken());
			int v = Integer.parseInt(st.nextToken());
		
			degrees[u]++;
			degrees[v]++;
			
			graph[u][v] = true;
			graph[v][u] = true;
		}
		
		backtracking(0, 1, new int[K]);
		
		System.out.println(-1);
	}	//	main-end
	
	private static void backtracking(int nth, int start, int[] clique) {
		if(nth == K) {
			for(int i = 0; i < K; i++)
				sb.append(clique[i]).append("\n");
			
			System.out.print(sb);
			System.exit(0);
		}
		
		for(int v = start; v <= N; v++) {
			if(degrees[v] < K - 1)	//	v와 연결된 정점이 K - 1개보다 적을 경우, v는 크기 K인 클리크의 일부일 수 없음
				continue;
			
			boolean isClique = true;
			for(int i = 0; i < nth; i++) {
				if(!graph[v][clique[i]]) {
					isClique = false;
					break;
				}
			}
			
			if(isClique) {	//	v는 이전 클리크를 이루는 이전 정점들과 모두 연결되어 있음
				clique[nth] = v;	//	v는 클리크를 이룸
				backtracking(nth + 1, v + 1, clique);	//	v 다음 정점들 중 클리크를 이룰 수 있는 것 찾음
			}
		}
	}
}	//	Main-class-end
```