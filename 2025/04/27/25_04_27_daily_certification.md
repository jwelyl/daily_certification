# 25_04_27_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2900 프로그램

[2900번: 프로그램](http://boj.ma/2900/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int K;
	private static int Q;
	
	private static final Map<Integer, Integer> map = new HashMap<>();

	private static int[] arr;
	private static long[] prefixSum;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		
		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
	
		arr = new int[N];
		prefixSum = new long[N];
	
		st = new StringTokenizer(br.readLine());
		for(int k = 0; k < K; k++) {
			int x = Integer.parseInt(st.nextToken());
			map.put(x, map.getOrDefault(x, 0) + 1);
		}
		
		for(Map.Entry<Integer, Integer> entry : map.entrySet()) {
			int x = entry.getKey();
			int cnt = entry.getValue();
			
			int q = N / x;
			int r = N % x;
			
			for(int i = 0; i < q; i++) {
				arr[i * x] += cnt;
				if(i * x + 1 < N)
					arr[i * x + 1] -= cnt;
			}
			
			if(r != 0) {
				arr[q * x] += cnt;
				if(q * x + 1 < N)
					arr[q * x + 1] -= cnt;
			}
		}
		
		for(int i = 0; i < N; i++) {
			if(i == 0)
				prefixSum[i] = arr[i];
			else {
				arr[i] = arr[i - 1] + arr[i];
				prefixSum[i] = prefixSum[i - 1] + arr[i];
			}
		}
		
		Q = Integer.parseInt(br.readLine());
		for(int q = 0; q < Q; q++) {
			st = new StringTokenizer(br.readLine());
			
			int l = Integer.parseInt(st.nextToken());
			int r = Integer.parseInt(st.nextToken());
			
			long ps = l == 0 ? prefixSum[r] : prefixSum[r] - prefixSum[l - 1]; 
			
			sb.append(ps).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
} //	Main-class-end
```