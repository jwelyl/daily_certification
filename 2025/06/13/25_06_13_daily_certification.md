# 25_06_13_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2481 해밍 경로

[2481번: 해밍 경로](http://boj.ma/2481/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import java.util.Queue;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int K;
	private static int M;
	
	//	Key : 이진 코드, Value : 해당 이진 코드를 가지고 있는 정점 번호
	private static final Map<Integer, Integer> codeMap = new HashMap<>();
	private static List<Integer>[] graph;
	
	private static int[] dists;
	private static int[] prevs;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		for(int i = 0; i <= N; i++)
			graph[i] = new ArrayList<Integer>();
		
		dists = new int[N + 1];
		prevs = new int[N + 1];
		Arrays.fill(dists, INF);
		
		for(int v = 1; v <= N; v++) {
			int code = Integer.parseInt(br.readLine(), 2);
			codeMap.put(code, v);
			
//			System.out.println("v = " + code);
			for(int k = 0; k < K; k++) {
				int mask = (1 << k);
				int masked = code & mask;
				
				if(masked != 0) {	//	k번째 비트가 1일 경우, k번째 비트를 0으로 바꿔야 함
					mask = ((1 << K) - 1) ^ (1 << k);
					masked = code & mask;
				}
				else	//	k번째 비트가 0일 경우, k번째 비트를 1로 바꿔야 함
					masked = code | mask;
				
//				System.out.println("masked = " + masked);
				
				int connected = codeMap.getOrDefault(masked, -1);
				
				if(connected != -1) {
					graph[v].add(connected);
					graph[connected].add(v);
				}
			}
		}
		
//		for(int v = 1; v <= N; v++)
//			System.out.println(graph[v]);
		
		bfs();
		
		M = Integer.parseInt(br.readLine());
		
		for(int q = 0; q < M; q++) {
			int dest = Integer.parseInt(br.readLine());
			
			if(dists[dest] == INF)
				sb.append("-1\n");
			else {
				Stack<Integer> stack = new Stack<>();
				
				int cur = dest;
				
				while(cur != 0) {
					stack.push(cur);
					cur = prevs[cur];
				}
				
				while(!stack.isEmpty())
					sb.append(stack.pop()).append(" ");
				sb.append("\n");
			}
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void bfs() {
		Queue<int[]> queue = new LinkedList<>();
		dists[1] = 0;
		queue.offer(new int[] {1, dists[1]});
		
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			
			int cv = cur[0];
			
			for(int nv : graph[cv]) {
				if(dists[nv] == INF) {
					dists[nv] = dists[cv] + 1;
					prevs[nv] = cv;
					queue.offer(new int[] {nv, dists[nv]});
				}
			}
		}
	}
}	//	Main-class-end
```