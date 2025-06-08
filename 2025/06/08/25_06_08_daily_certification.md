# 25_06_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15586 MooTube (Gold)

[15586번: MooTube (Gold)](http://boj.ma/15586/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int Q;
		
	private static int edgeIdx = 0;
	private static final List<int[]> edges = new ArrayList<>();
	private static int[][] queries;
	
	private static int[] parents;
	private static int[] dsSizes;
	
	private static int[] answer;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		for(int v = 1; v <= N; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		queries = new int[Q][3];
		answer = new int[Q];
		
		for(int e = 0; e < N - 1; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			edges.add(new int[] {v1, v2, c});
		}
		
		//	간선이 큰 순서대로 간선 정렬
		Collections.sort(edges, (e1, e2) -> Integer.compare(e2[2], e1[2]));
		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			queries[q][0] = q;
			queries[q][1] = Integer.parseInt(st.nextToken());
			queries[q][2] = Integer.parseInt(st.nextToken());
		}
		
		//	k가 큰 순서대로 쿼리 정렬
		Arrays.sort(queries, (q1, q2) -> Integer.compare(q2[1], q1[1]));
		
		for(int[] query : queries) {
			int idx = query[0];		//	원래 idx번째 쿼리였음
			int k = query[1];
			int v = query[2];		//	v로부터 USADO가 k인 정점 개수 찾아야 함
			
			while(edgeIdx < N - 1) {
				int[] edge = edges.get(edgeIdx);
				
				int v1 = edge[0];
				int v2 = edge[1];
				int c = edge[2];
				
				if(c < k)	//	간선 비용이 k보다 작은 간선이 끼어들면 USADO k인 정점 찾기 어려움
					break;
				
				union(v1, v2);
				edgeIdx++;
			}
			
			answer[idx] = dsSizes[find(v)] - 1;
		}
		
		for(int ans : answer)
			sb.append(ans).append("\n");
		
		System.out.print(sb);
	} //	main-end
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			parents[v2] = v1;
			dsSizes[v1] += dsSizes[v2];
			dsSizes[v2] = 0;
		}
	}
} //	Main-class-end
```