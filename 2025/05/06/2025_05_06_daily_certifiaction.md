# 25_05_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10888 두 섬간의 이동

[10888번: 두 섬간의 이동](http://boj.ma/10888/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	
	private static int N;
	
	private static int[] parents;
	private static int[] dsSizes;
	
	private static long answer1 = 0;	//	현재 상태에서 왕래 가능한 두 섬 (i, j) 쌍 (i < j)의 개수
	private static long answer2 = 0;	//	현재 상태에서 왕래 가능한 두 섬 (i, j) 쌍 (i < j)에 대해 최소 간선 수의 합
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		for(int v = 1; v <= N; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
	
		for(int e = 0; e < N - 1; e++) {
			int v1 = Integer.parseInt(br.readLine());
			int v2 = v1 + 1;
			
			v1 = find(v1);
			v2 = find(v2);
			
			int cnt1 = dsSizes[v1];
			int cnt2 = dsSizes[v2];
			
			answer1 -= (getAnswer1(cnt1) + getAnswer1(cnt2));
			answer1 += (getAnswer1(cnt1 + cnt2));
			answer2 -= (getAnswer2(cnt1) + getAnswer2(cnt2));
			answer2 += (getAnswer2(cnt1 + cnt2));
		
			union(v1, v2);
			
			sb.append(answer1).append(" ").append(answer2).append("\n");
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
			dsSizes[v1] += dsSizes[v2];
			dsSizes[v2] = 0;
		}
	}
	
	private static long getAnswer1(int cnt) {
		return (long)cnt * (cnt - 1) / 2;
	}
	
	private static long getAnswer2(int cnt) {
		return (long)cnt * (cnt - 1) * (cnt + 1) / 6;
	}
} //	Main-class-end
```