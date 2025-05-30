# 25_05_30_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 18085 Firetrucks Are Red

[18085번: Firetrucks Are Red](http://boj.ma/18085/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.StringTokenizer;
import java.util.TreeMap;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;

	private static int[] parents;
	private static int edgeCnt = 0;

	//	Key : 간선 비용 cost, Value : 비용이 cost인 간선을 가지는 정점 리스트
	private static final Map<Integer, List<Integer>> map = new TreeMap<>();
	private static final List<int[]> edges = new ArrayList<>();
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		parents = new int[N + 1];
		for(int v = 1; v <= N; v++)
			parents[v] = v;
		
		for(int v = 1; v <= N; v++) {
			st = new StringTokenizer(br.readLine());
			int m = Integer.parseInt(st.nextToken());
			
			for(int i = 0; i < m; i++) {
				int cost = Integer.parseInt(st.nextToken());
				List<Integer> list = map.getOrDefault(cost, new ArrayList<>());
				
				list.add(v);
				map.put(cost, list);
			}
		}
		
		OUTER:
		for(int cost : map.keySet()) {
			List<Integer> list = map.get(cost);
			int len = list.size();
			
			for(int i = 0; i < len - 1; i++) {
				int v1 = list.get(i);
				int v2 = list.get(i + 1);
				
				if(find(v1) != find(v2)) {
					union(v1, v2);
					edgeCnt++;
					edges.add(new int[] {v1, v2, cost});
					
					if(edgeCnt == N - 1)
						break OUTER;
				}
			}
		}
	
		if(edgeCnt == N - 1) {
			for(int[] edge : edges)
				sb.append(edge[0]).append(" ").append(edge[1]).append(" ").append(edge[2]).append("\n");
		}
		else
			sb.append("impossible\n");
		System.out.print(sb);
	} //	main-end
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2)
			parents[v2] = v1;
	}
} //	Main-class-end
```