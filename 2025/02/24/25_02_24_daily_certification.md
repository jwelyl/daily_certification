# 25_02_24_daily_certification

```
[#054 koreii] 데일리인증 20250223
0. 경력 정리 + 자기소개서 작성
1. 코딩 테스트 대비 알고리즘 학습
- LCA, Prefix Sum(imos) (BOJ 26615)
2. SQLD
- SQL 기본 및 활용 복습 + 문제풀이
```

# Problem Solving (Algorithm & SQL)

### BOJ 26615 다오의 행사 계획하기

[26615번: 다오의 행사 계획하기](http://boj.ma/26615/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX_H = 100_001;
	private static final int MAX_A = (int)Math.ceil(Math.log(MAX_H) / Math.log(2)) + 1; 
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int T;
	private static int K;
	
	private static List<Integer>[] tree;
	private static int[] depth;
	
	private static int[][] ancestors;
	
	private static long[] prefixSum;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		T = Integer.parseInt(st.nextToken());
		
		tree = new ArrayList[N * M + 1];
		for(int v = 0; v <= N * M; v++)
			tree[v] = new ArrayList<>();
		
		depth = new int[N * M + 1];
		ancestors = new int[MAX_A][N * M + 1];
		prefixSum = new long[T + 2];
		
		inputTree();
		dfs(1, 0, 1);
		makeAncestors();
		
		imos();
	}	//	main-end
	
	private static int dist(int v1, int v2) {
		int deeper = depth[v1] > depth[v2] ? v1 : v2;
		int shallower = deeper == v1 ? v2 : v1;
		int diff = depth[deeper] - depth[shallower];
		int exp = 0;
		
		int lca = 0;
		
		while(diff > 0) {
			int r = diff & 1;
			diff >>= 1;
			
			if(r == 1)
				deeper = ancestors[exp][deeper];
			
			exp++;
		}
		
		if(deeper == shallower)
			lca = deeper;
		else {
			for(int i = MAX_A - 1; i >= 0; i--) {
    			int x = ancestors[i][deeper];
    			int y = ancestors[i][shallower];
    			
    			if(x != y) {
    				deeper = x;
    				shallower = y;
    			}
    		}
			
			lca = ancestors[0][deeper];
		}
        
		return depth[v1] + depth[v2] - 2 * depth[lca];
	}
	
	private static void imos() throws IOException {
		K = Integer.parseInt(br.readLine());
		for(int k = 0; k < K; k++) {
			st = new StringTokenizer(br.readLine());
			int s = Integer.parseInt(st.nextToken());
			int e = Integer.parseInt(st.nextToken());
			int a = Integer.parseInt(st.nextToken());
			int b = Integer.parseInt(st.nextToken());
			int c = Integer.parseInt(st.nextToken());
			int d = Integer.parseInt(st.nextToken());
			int v = Integer.parseInt(st.nextToken());
			
			int v1 = a * M + b + 1;
			int v2 = c * M + d + 1;
			
			int cnt = dist(v1, v2) + 1;
			
			prefixSum[s] += cnt * v;
			prefixSum[e + 1] -= cnt * v;
		}
		
		for(int v = 1; v <= T; v++) {
			prefixSum[v] += prefixSum[v - 1];
			sb.append(prefixSum[v]).append("\n");
		}
		
		System.out.print(sb);
	}
	
	private static void makeAncestors() {
		for(int h = 1; h < MAX_A; h++) {
			for(int v = 1; v <= N * M; v++)
				//	v의 2^h번째 조상은 v의 2^(h-1)번째 조상의 2^(h-1)번째 조상
				ancestors[h][v] = ancestors[h - 1][ancestors[h - 1][v]];
		}
	}
	
	private static void dfs(int vertex, int p, int d) {
		ancestors[0][vertex] = p;	//	vertex의 2^0번째 조상(부모)가 p임
		depth[vertex] = d;
		
		for(int next : tree[vertex]) {
			if(depth[next] == 0)
				dfs(next, vertex, d + 1);
		}
	}
	
	private static void inputTree() throws IOException {
		for(int y = 0; y < N - 1; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M; x++) {
				int v1 = y * M + x + 1;
				int v2 = (y + 1) * M + x + 1;
				
				int wall = Integer.parseInt(st.nextToken());
				
				if(wall == 0) {
					tree[v1].add(v2);
					tree[v2].add(v1);
				}
			}
		}
		
		for(int y = 0; y < N; y++) {
			st = new StringTokenizer(br.readLine());
			for(int x = 0; x < M - 1; x++) {
				int v1 = y * M + x + 1;
				int v2 = v1 + 1;
				
				int wall = Integer.parseInt(st.nextToken());
				
				if(wall == 0) {
					tree[v1].add(v2);
					tree[v2].add(v1);
				}
			}
		}
	}
}	//	Main-class-end
```