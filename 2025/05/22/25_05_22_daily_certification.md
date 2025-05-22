# 25_05_22_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 25587 배수로

[25587번: 배수로](http://boj.ma/25587/t)

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
	private static int[] dsSizes;
	private static int[] aSums;
	private static int[] bSums;
	
	private static int answer;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		parents = new int[N + 1];
		dsSizes = new int[N + 1];
		aSums = new int[N + 1];
		bSums = new int[N + 1];
		
		for(int v = 0; v <= N; v++) {
			parents[v] = v;
			dsSizes[v] = 1;
		}
		
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++)
			aSums[v] = Integer.parseInt(st.nextToken());
		st = new StringTokenizer(br.readLine());
		for(int v = 1; v <= N; v++) {
			bSums[v] = Integer.parseInt(st.nextToken());
			if(bSums[v] > aSums[v])
				answer++;
		}
		
		for(int q = 0; q < M; q++) {
			st = new StringTokenizer(br.readLine());
			int opt = Integer.parseInt(st.nextToken());
			
			if(opt == 1) {
				int v1 = Integer.parseInt(st.nextToken());
				int v2 = Integer.parseInt(st.nextToken());
				
				union(v1, v2);
			}
			else
				sb.append(answer).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static int find(int v) {
		return v == parents[v] ? v : (parents[v] = find(parents[v]));
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			int size1 = dsSizes[v1];	//	합쳐지기 전 v1이 속한 도시 집합의 도시 개수
			int size2 = dsSizes[v2];	//	합쳐지기 전 v2가 속한 도시 집합의 도시 개수
			int a1 = aSums[v1];
			int b1 = bSums[v1];			//	합쳐지기 전 v1이 속한 도시 집합의 배수로 용량 합, 강수량 합
			int a2 = aSums[v2];
			int b2 = bSums[v2];			//	합쳐지기 전 v2가 속한 도시 집합의 배수로 용량 합, 강수량 합
			
			//	v2가 속한 도시들이 v1이 속한 도시들과 합쳐짐
			dsSizes[v1] += dsSizes[v2];
			dsSizes[v2] = 0;
			parents[v2] = v1;
			aSums[v1] += aSums[v2];
			aSums[v2] = 0;
			bSums[v1] += bSums[v2];
			bSums[v2] = 0;
			
			if(aSums[v1] >= bSums[v1]) {	//	합쳐져서 홍수 막을 수 있게 된 경우
				if(a1 < b1)	//	합쳐지기 전에는 v1에서는 홍수를 막을 수 없었던 경우
					answer -= size1;
				else if(a2 < b2)	//	합쳐지기 전에는 v2에서는 홍수를 막을 수 없었던 경우
					answer -= size2;
			}
			else {	//	합쳐져서 홍수를 막을 수 없게 된 경우
				if(a1 >= b1)	//	합쳐지기 전에는 v1에서는 홍수를 막을 수 있었던 경우
					answer += size1;
				else if(a2 >= b2)	//	합쳐지기 전에는 v2에서는 홍수를 막을 수 있었던 경우
					answer += size2;
			}
		}
	}
}	//	Main-class-end
```