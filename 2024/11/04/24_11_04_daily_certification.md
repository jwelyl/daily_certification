# 24_11_04_daily_certification

# Problem Solving (Algorithm & SQL)

### **CO{)E TREE 경로의 적합성 판단 2**

[코드트리 | 코딩테스트 준비를 위한 알고리즘 정석](https://www.codetree.ai/missions/9/problems/determining-the-suitability-of-the-route-2/description)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int K;
	
	private static int[] parents;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());

		parents = new int[N + 1];
		for(int v = 1; v <= N; v++)
			parents[v] = v;
		
		for(int m = 0; m < M; m++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			union(v1, v2);
		}
		
		st = new StringTokenizer(br.readLine());
		int cur = Integer.parseInt(st.nextToken());
		
		for(int k = 0; k < K - 1; k++) {
			int next = Integer.parseInt(st.nextToken());
			
			if(find(cur) != find(next)) {
				System.out.println(0);
				return;
			}
			
			cur = next;
		}
		
		System.out.println(1);
	}	//	main-end
	
	private static int find(int v) {
		if(v == parents[v])
			return v;
		
		return parents[v] = find(parents[v]);
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2)
			parents[v2] = v1;
	}
}	//	Main-class-end
```