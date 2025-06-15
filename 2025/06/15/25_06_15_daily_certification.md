# 25_06_15_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 28277 뭉쳐야 산다

[28277번: 뭉쳐야 산다](http://boj.ma/28277/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashSet;
import java.util.Set;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int Q;

	private static Set<Integer>[] sets;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());

		sets = new HashSet[N + 1];
		
		for (int v = 1; v <= N; v++)
			sets[v] = new HashSet<>();
		
		for(int v = 1; v <= N; v++) {
			st = new StringTokenizer(br.readLine());
			int s = Integer.parseInt(st.nextToken());
			for(int i = 0; i < s; i++)
				sets[v].add(Integer.parseInt(st.nextToken()));
		}
		
		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			int query = Integer.parseInt(st.nextToken());
			int a = Integer.parseInt(st.nextToken());
			int b;
			
			if(query == 1) {
				b = Integer.parseInt(st.nextToken());
				
				if(sets[a].size() > sets[b].size()) {
					sets[a].addAll(sets[b]);
					sets[b].clear();
				}
				else {
					sets[b].addAll(sets[a]);
					sets[a] = sets[b];
					sets[b] = new HashSet<>();
				}
			}
			else
				sb.append(sets[a].size()).append("\n");
 		}
		
		System.out.print(sb);
	}	//	main-end
}	//	Main-class-end

```