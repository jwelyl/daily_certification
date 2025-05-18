# 25_05_18_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15508 Xayahh-Rakann at Moloco (Easy)

[15508번: Xayahh-Rakann at Moloco (Easy)](http://boj.ma/15508/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N; 
	private static int M;
	private static int K;

	private static int[] parents;
	private static int[] dsSizes;
	
	private static boolean find = false;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		
		for(int v = 1; v <= N; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			union(v1, v2);
		}
		
		subset(0, new boolean[N]);
		
		System.out.println(find ? "SAFE" : "DOOMED");
	}	//	main-end
	
	private static void subset(int nth, boolean[] selected) {
		if(find)
			return;
		
		if(nth == N) {	//	N개를 두 집합으로 나눴을 경우
			Set<Integer> dsSet = new HashSet<>();
			int size = 0;
			
			for(int i = 0; i < N; i++) {
				if(selected[i]) {
					int ds = find(i + 1);
					
					if(!dsSet.contains(ds)) {
						dsSet.add(ds);
						size += dsSizes[ds];
					}
				}
			}
			
			if(size == K || size == N - K)
				find = true;
			
			return;
		}
		
		selected[nth] = true;
		subset(nth + 1, selected);
		selected[nth] = false;
		subset(nth + 1, selected);
	}
	
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
}	//	Main-class-end
```