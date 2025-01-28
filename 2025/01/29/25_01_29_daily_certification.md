# 25_01_29_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 33230 Bitcoin Bubble**

[33230번: Bitcoin Bubble](http://boj.ma/33230/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;

	private static int N;
	
	private static int[] prices;	//	prices[i] : i번째 비트코인 가격
	private static int[] days;		//	days[i] : i번째 비트코인의 지속 기간
	
	private static int[] minSegtree;	//	minSegtree[node] : node가 담당하는 [start, end] 비트코인 중 최소가격인 비트코인의 index
	private static long[] psSegtree;	//	psSegtree[node] : node가 담당하는 [start, end] 비트코인의 지속 기간 누적합
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		prices = new int[N + 1];
		days = new int[N + 1];
		minSegtree = new int[4 * N + 4];
		psSegtree = new long[4 * N + 1];
		
		prices[0] = Integer.MAX_VALUE;
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			prices[i] = Integer.parseInt(st.nextToken());
			days[i] = Integer.parseInt(st.nextToken());
		}
		
		init(1, 1, N);
		
		System.out.println(recursion(1, N));
	}	//	main-end
	
	private static long recursion(int start, int end) {
		int minIdx = minIdx(1, 1, N, start, end);		//	[start, end] 비트코인 최소 가격의 index
		long ps = partialSum(1, 1, N, start, end);		//	[start, end] 비트코인 지속기간의 누적합
		
		long res = ps * prices[minIdx];
		
		if(start < minIdx)
			res = Math.max(res, recursion(start, minIdx - 1));
		if(end > minIdx)
			res = Math.max(res, recursion(minIdx + 1, end));
		
		return res;
	}
	
	private static void init(int node, int start, int end) {
		if(start == end) {
			minSegtree[node] = start;
			psSegtree[node] = days[start];
			return;
		}
		
		int mid = (start + end) / 2;
		int lc = 2 * node;
		int rc = 2 * node + 1;
		
		init(lc, start, mid);
		init(rc, mid + 1, end);

		int leftIdx = minSegtree[lc];
		int rightIdx = minSegtree[rc];
		long leftSum = psSegtree[lc];
		long rightSum = psSegtree[rc];
		
		minSegtree[node] = prices[leftIdx] > prices[rightIdx] ? rightIdx : leftIdx;
		psSegtree[node] = leftSum + rightSum;
	}
	
	private static int minIdx(int node, int start, int end, int left, int right) {
		if(right < start || end < left)
			return 0;
		
		if(left <= start && end <= right)
			return minSegtree[node];
		
		int mid = (start + end) / 2;
		int lc = 2 * node;
		int rc = 2 * node + 1;
		
		int leftIdx = minIdx(lc, start, mid, left, right);
		int rightIdx = minIdx(rc, mid + 1, end, left, right);
		
		return prices[leftIdx] > prices[rightIdx] ? rightIdx : leftIdx;
	}
	
	private static long partialSum(int node, int start, int end, int left, int right) {
		if(right < start || end < left)
			return 0;
		
		if(left <= start && end <= right)
			return psSegtree[node];
	
		int mid = (start + end) / 2;
		int lc = 2 * node;
		int rc = 2 * node + 1;
		
		long leftSum = partialSum(lc, start, mid, left, right);
		long rightSum = partialSum(rc, mid + 1, end, left, right);
		
		return leftSum + rightSum;
	}
}	//	Main-class-end
```