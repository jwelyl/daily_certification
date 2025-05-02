# 25_05_03_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 15789 **CTP 왕국은 한솔 왕국을 이길 수 있을까?**

[15789번: CTP 왕국은 한솔 왕국을 이길 수 있을까?](http://boj.ma/15789/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int C;
	private static int H;
	private static int K;

	private static int[] parents;
	private static int[] dsSizes;
	
	private static final List<int[]> dsList = new ArrayList<>();
	
	private static int maxPower = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
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
		
		for(int v = 1; v <= N; v++) {
			if(parents[v] == v)
				dsList.add(new int[] {v, dsSizes[v]});
		}
		
		//	힘이 센 연합 순으로 정렬
		Collections.sort(dsList, (ds1, ds2) -> Integer.compare(ds2[1], ds1[1]));
		
		st = new StringTokenizer(br.readLine());
		C = Integer.parseInt(st.nextToken());
		H = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		C = find(C);	//	CTP 왕국 속한 연합 우두머리
		maxPower = dsSizes[C];	//	CTP 왕국 속한 연합의 힘
		H =	find(H);	//	한솔이가 속한 연합 우두머리
		
		for(int i = 0; i < dsList.size() && K > 0; i++) {
			int[] ds = dsList.get(i);
			int dsV = ds[0];
			int dsPower = ds[1];
			
			if(dsV == H || dsV == C)
				continue;
			
			maxPower += dsPower;
			K--;
		}
		
		System.out.println(maxPower);
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
} //	Main-class-end
```