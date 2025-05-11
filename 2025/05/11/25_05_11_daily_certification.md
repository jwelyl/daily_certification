# 25_05_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 32203 연락

[32203번: 연락](http://boj.ma/32203/t)

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
	private static int[] nMale;
	private static int[] nFemale;
	
	private static long answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		nMale = new int[N + 1];
		nFemale = new int[N + 1];
		
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++) {
			int sex = Integer.parseInt(st.nextToken()) % 2;
			
			if(sex == 1)
				nMale[v] = 1;
			else
				nFemale[v] = 1;
			
			parents[v] = v;
		}
		
		for(int q = 0; q < M; q++) {
			st = new StringTokenizer(br.readLine());
			int v1 = find(Integer.parseInt(st.nextToken()));
			int v2 = find(Integer.parseInt(st.nextToken()));
			
			if(v1 != v2) {
				answer -= (nPair(v1) + nPair(v2));
				union(v1, v2);
				v1 = find(v1);
				answer += nPair(v1);
			}
			
			sb.append(answer).append("\n");
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
			parents[v2] = v1;
			nFemale[v1] += nFemale[v2];
			nMale[v1] += nMale[v2];
			nFemale[v2] = 0;
			nMale[v2] = 0;
		}
	}
	
	private static long nPair(int v) {
		v = find(v);
		return (long)nFemale[v] * nMale[v];
	}
} //	Main-class-end
```