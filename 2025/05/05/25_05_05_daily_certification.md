# 25_05_05_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 18267 **Milk Visits**

[18267번: Milk Visits](http://boj.ma/18267/t)

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
	private static int M;
	private static int[] parents;
	private static char[] type;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		type = br.readLine().toCharArray();
		for(int v = 1; v <= N; v++)
			parents[v] = v;
		
		for(int e = 0; e < N - 1; e++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			if(type[v1 - 1] == type[v2 - 1])
				union(v1, v2);
		}
		
		for(int q = 0; q < M; q++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			char t = st.nextToken().charAt(0);
			
			v1 = find(v1);
			v2 = find(v2);
			
			if(v1 == v2)
				sb.append(type[v1 - 1] == t ? 1 : 0);
			else
				sb.append(1);
		}
		
		System.out.println(sb);
	}	//	main-end
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2)
			parents[v2] = v1;
	}
}	//	Main-class-end

```