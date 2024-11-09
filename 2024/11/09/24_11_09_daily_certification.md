# 24_11_09_daily_certification

```
[#314 koreii] 데일리인증 20241109
1. Spring Core Advanced
- Spring Bean
- BeanPostProcessor
- AspectJExpressionPointcut
2. 코딩 테스트 대비 복습문제 풀이
-  DFS, Tree (트리의 지름)
```

# Problem Solving (Algorithm & SQL)

### **CO{)E TREE 트리의 지름**

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/diameter-of-tree/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	
	private static List<int[]>[] tree;
	private static int[] dist;
	
	private static int root = 0;
	private static int maxDist = 0;		//	root에서 가장 먼 정점까지의 거리
	private static int maxDistV = 0;	//	root에서 가장 먼 정점
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
	
		tree = new ArrayList[N + 1];
		for(int v = 1; v <= N; v++)
			tree[v] = new ArrayList<>();
		dist = new int[N + 1];
		
		for(int m = 0; m < N - 1; m++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			
			tree[v1].add(new int[] {v2, c});
			tree[v2].add(new int[] {v1, c});
		}
		
		root = 1;
		Arrays.fill(dist, -1);
		dfs(root, 0);
		
		maxDist = 0;
	
		root = maxDistV;
		Arrays.fill(dist, -1);
		dfs(root, 0);
		
		System.out.println(maxDist);
	} //	main-end
	
	private static void dfs(int v, int d) {
		dist[v] = d;
		
		if(dist[v] > maxDist) {
			maxDist = dist[v];
			maxDistV = v;
		}
		
		for(int[] edge : tree[v]) {
			int vertex = edge[0];
			int cost = edge[1];
			
			if(dist[vertex] == -1)
				dfs(vertex, d + cost);
		}
	}
} //	Main-class-end
```