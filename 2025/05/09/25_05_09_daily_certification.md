# 25_05_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 18921 Cost Of Subtree

[18921번: Cost Of Subtree](http://boj.ma/18921/t)

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
	private static StringTokenizer st;
	
	private static int N;
	private static final List<int[]> edges = new ArrayList<>();
	
	private static int[] parents;
	private static int[] dsSizes;	//	dsSizes[v] : v가 속한 서버트리의 정점 개수 (간선 개수 + 1)
	
	private static long answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		
		for(int v = 1; v <= N; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		for(int e = 0; e < N - 1; e++) {
			st = new StringTokenizer(br.readLine());
			edges.add(new int[] {Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken()), Integer.parseInt(st.nextToken())});
		}
		
		Collections.sort(edges, (e1, e2) -> Integer.compare(e2[2], e1[2]));
		
		for(int[] edge : edges) {
			int v1 = find(edge[0]);
			int v2 = find(edge[1]);
			int c = edge[2];
			
			union(v1, v2);
			
			answer = Math.max(answer, (long)c * (dsSizes[find(v1)] - 1));
		}
		
		System.out.println(answer);
	} //	main-end
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		parents[v2] = v1;
		dsSizes[v1] += dsSizes[v2];
		dsSizes[v2] = 0;
	}
} //	Main-class-end
```