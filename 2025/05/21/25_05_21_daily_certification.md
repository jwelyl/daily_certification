# 25_05_21_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 19538 루머

[19538번: 루머](http://boj.ma/19538/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static List<Integer>[] graph;
	private static int[] times;
	private static int[] rumors;	//	rumors[v] : v 주변 사람 중 루머 믿는 사람 수
	
	private static final Queue<int[]> queue = new LinkedList<>();
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		graph = new ArrayList[N + 1];
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		times = new int[N + 1];
		Arrays.fill(times, NONE);
		rumors = new int[N + 1];
		
		for(int v = 1; v <= N; v++) {
			st = new StringTokenizer(br.readLine());
			
			while(st.hasMoreTokens()) {
				int nv = Integer.parseInt(st.nextToken());
				
				if(nv != 0)
					graph[v].add(nv);
			}
		}
		
		M = Integer.parseInt(br.readLine());
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < M; i++) {
			int v = Integer.parseInt(st.nextToken());
			times[v] = 0;
			queue.offer(new int[] {v, 0});
		}
		
		bfs();
	} //	main-end
	
	private static void bfs() {
		while(!queue.isEmpty()) {
			int[] cur = queue.poll();
			int cv = cur[0];
			int ct = cur[1];
			
			for(int nv : graph[cv]) {
				if(times[nv] == NONE) {
					rumors[nv]++;
					
					if(rumors[nv] >= (graph[nv].size() + 1) / 2) {
						times[nv] = ct + 1;
						queue.offer(new int[] {nv, times[nv]});
					}
				}
			}
		}
		
		for(int v = 1; v <= N; v++)
			sb.append(times[v] == NONE ? -1 : times[v]).append(" ");
		sb.append("\n");
		
		System.out.print(sb);
	}
} //	Main-class-end
```