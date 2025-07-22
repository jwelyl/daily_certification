# 25_07_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20927 Degree Bounded Minumum Spanning Tree

[20927번: Degree Bounded Minimum Spanning Tree](http://boj.ma/20927/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final int NONE = 10_000_000 * 27; 
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;					//	그래프 정점 개수 N
	private static int M;					//	그래프 간선 개수 M
	
	private static int[] boundLimits;		//	boundLimits[v] : 신장트리에서 v 정점의 degree 제한
	private static int[][] edges;
	
	private static int mstCost = NONE;	//	만들어진 신장트리 비용 중 최소인 것의 비용
	private static int[] mstEdgeList;	//	그때의 선택된 간선 리스트
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		boundLimits = new int[N + 1];
		edges = new int[M + 1][3];
		mstEdgeList =  new int[N - 1];
		
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++)
			boundLimits[v] = Integer.parseInt(st.nextToken());
		
		for(int e = 1; e <= M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			edges[e][0] = v1 < v2 ? v1 : v2;
			edges[e][1] = v1 < v2 ? v2 : v1;
			edges[e][2] = c;
		}
		
		backtracking(0, 1, new int[N - 1]);
		
		sb.append(mstCost == NONE ? "NO\n" : "YES\n");
		
		if(mstCost != NONE) {
			for(int e : mstEdgeList) {
				int[] edge = edges[e];
				
				sb.append(edge[0]).append(" ").append(edge[1]).append("\n");
			}
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static void backtracking(int nth, int start, int[] edgeList) {
		if(nth == N - 1) {	//	N - 1개의 간선을 모두 선택한 경우
			int res = makeTree(edgeList);
			
			if(res < mstCost) {
				mstCost = res;
				for(int i = 0; i < N - 1; i++)
					mstEdgeList[i] = edgeList[i];
			}
			
			return;
		}
		
		for(int e = start; e <= M; e++) {
			edgeList[nth] = e;
			backtracking(nth + 1, e + 1, edgeList);
		}
	}
	
	//	edgeList의 간선을 모두 연결했을때 신장트리가 되는지 확인
	//	사이클 발생 시 NONE return
	//	간선 연결 시 제한 degree 초과 시 NONE return
	private static int makeTree(int[] edgeList) {
		int res = 0;
		int[] bounds = new int[N + 1];
		
		int[] parents = new int[N + 1];
		for(int v = 1; v <= N; v++)
			parents[v] = v;
		
		for(int e : edgeList) {
			int[] edge = edges[e];
			int v1 = edge[0];
			int v2 = edge[1];
			int c = edge[2];
			
			if(find(v1, parents) == find(v2, parents))
				return NONE;
			
			res += c;
			union(v1, v2, parents);	//	v1과 v2를 연결
			
			bounds[v1]++;
			bounds[v2]++;
			
			if(boundLimits[v1] < bounds[v1] || boundLimits[v2] < bounds[v2])
				return NONE;
		}
		
		return res;
	}
	
	private static int find(int v, int[] parents) {
		return v == parents[v] ? v : (parents[v] = find(parents[v], parents)); 
	}
	
	private static void union(int v1, int v2, int[] parents) {
		v1 = find(v1, parents);
		v2 = find(v2, parents);
		
		if(v1 != v2)
			parents[v2] = v1;
	}
} //	Main-class-end
```