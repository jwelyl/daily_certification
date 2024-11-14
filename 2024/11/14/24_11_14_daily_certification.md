# 24_11_14_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 14167 Moocast

[14167번: Moocast](https://boj.ma/14167/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;

	private static int[] xpos;
	private static int[] ypos;
	private static int[] parents;
	
	private static final List<long[]> edges = new ArrayList<>();
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		xpos = new int[N + 1];
		ypos = new int[N + 1];
		parents = new int[N + 1];
		for(int v = 1; v <= N; v++)
			parents[v] = v;
		
		for(int v = 1; v <= N; v++) {
			st = new StringTokenizer(br.readLine());
			xpos[v] = Integer.parseInt(st.nextToken());
			ypos[v] = Integer.parseInt(st.nextToken());
		}
		
		for(int v1 = 1; v1 < N; v1++) {
			for(int v2 = v1 + 1; v2 <= N; v2++)
				edges.add(new long[] {v1, v2, dist(v1, v2)});
		}
		
		System.out.println(kruskal());
	}	//	main-end
	
	private static long dist(int v1, int v2) {
		return (xpos[v1] - xpos[v2]) * (xpos[v1] - xpos[v2]) + (ypos[v1] - ypos[v2]) * (ypos[v1] - ypos[v2]);
	}

	private static int findDS(int v) {
		if(v == parents[v])
			return v;
		
		return parents[v] = findDS(parents[v]);
	}
	
	private static void union(int v1, int v2) {
		parents[v2] = v1;
	}
	
	private static long kruskal() {
		long res = 0;
		int edgeCnts = 0;
		
		edges.sort((e1, e2) -> Long.compare(e1[2], e2[2]));
		
		for(long[] edge : edges) {
			int v1 = findDS((int)edge[0]);
			int v2 = findDS((int)edge[1]);
			long cost = edge[2];
			
			if(v1 != v2) {
				union(v1, v2);
				res = cost;
				edgeCnts++;
			}
			
			if(edgeCnts == N - 1)
				break;
		}
		
		return res;
	}
}	//	Main-class-end
```