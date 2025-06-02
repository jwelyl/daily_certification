# 25_06_02_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 28251 나도리합

[28251번: 나도리합](http://boj.ma/28251/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int Q;
	
	private static int[] sizes;
	private static long[] powers;
	private static int[] parents;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		Q = Integer.parseInt(st.nextToken());
		
		sizes = new int[N + 1];
		powers = new long[N + 1];
		parents = new int[N + 1];
		
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++) {
			sizes[v] = Integer.parseInt(st.nextToken());
			parents[v] = v;
		}

		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			union(v1, v2);

			sb.append(powers[find(v1)]).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			powers[v1] += powers[v2];
			powers[v1] += (long)sizes[v1] * sizes[v2];
			sizes[v1] += sizes[v2];
			sizes[v2] = 0;
			
			parents[v2] = v1;
		}
	}
} //	Main-class-end
```