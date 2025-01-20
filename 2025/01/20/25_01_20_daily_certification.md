# 25_01_20_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ** 22959 신촌 수열과 쿼리

[22959번: 신촌 수열과 쿼리](https://boj.ma/22959/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	
	private static int[] arr;
	
	private static int H;		//	세그먼트 트리
	private static int size;	//	세그먼트 트리 크기
	
	private static int[] minSegtree;			//	최솟값 세그먼트 트리
	private static long[] psSegtree;			//	구간합 세그먼트 트리
	
	private static int M;	//	쿼리 개수
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		arr = new int[N];
		
		st = new StringTokenizer(br.readLine());
		for(int i = 0; i < N; i++)
			arr[i] = Integer.parseInt(st.nextToken());
		
		H = (int)Math.ceil(Math.log(N) / Math.log(2)) + 1;
		size = 1 << H;
		
		minSegtree = new int[size];
		psSegtree = new long[size];
		
		initMinSegtree(1, 0, N - 1);
		initPsSegtree(1, 0, N - 1);
		
		M = Integer.parseInt(br.readLine());
		
		for(int q = 0; q < M; q++) {
			st = new StringTokenizer(br.readLine());
			
			int opt = Integer.parseInt(st.nextToken());
			int i = Integer.parseInt(st.nextToken()) - 1;
			int j = Integer.parseInt(st.nextToken());
			
			if(opt == 1)
				update(1, 0, N - 1, i, j);
			else {
				int leftBound = i;
				int rightBound = i;	//	arr[i]는 항상 j 이상임
				
				int start = 0;
				int end = i - 1;	//	i보다 왼쪽에서 찾기
				
				while(start <= end) {
					int mid = (start + end) / 2;
					
					//	[start, mid]에서 arr의 최솟값
					int minCost = minCost(1, 0, N - 1, mid, i);
					
					if(minCost >= j) {	//	[mid, i] 구간에서 arr의 값이 모두 j 이상일 경우
						leftBound = mid;
						end = mid - 1;	//	범위 더 넓혀보기
					}
					else	//	[start, mid] 구간에서 j보다 작은 값이 있을 경우
						start = mid + 1;	//	범위 더 좁혀보기
				}
				
				start = i + 1;
				end = N - 1;		//	i보다 오른쪽에서 찾기
				
				while(start <= end) {
					int mid = (start + end) / 2;
					
					//	[start, mid]에서 arr의 최솟값
					int minCost = minCost(1, 0, N - 1, i, mid);
					
					if(minCost >= j) {	//	[start, mid] 구간에서 arr의 값이 모두 j 이상일 경우
						rightBound = mid;
						start = mid + 1;	//	범위 더 넓혀보기
					}
					else	//	[start, mid] 구간에서 j보다 작은 값이 있을 경우
						end = mid - 1;	//	범위 더 좁혀보기
				}
				
				sb.append(partSum(1, 0, N - 1, leftBound, rightBound)).append("\n");
			}
		}
		
		System.out.println(sb);
	}	//	main-end
	
	private static void initMinSegtree(int node, int start, int end) {
		if(start == end)
			minSegtree[node] = arr[start];
		else {
			//	left-subtree init
			initMinSegtree(2 * node, start, (start + end) / 2);
			//	right-subtree init
			initMinSegtree(2 * node + 1, (start + end) / 2 + 1, end);
			
			minSegtree[node] = Math.min(minSegtree[2 * node], minSegtree[2 * node + 1]);
		}
	}
	private static int minCost(int node, int start, int end, int left, int right) {
		if(right < start || left > end)
			return Integer.MAX_VALUE;
		
		if(left <= start && end <= right)
			return minSegtree[node];
		
		int leftMinCost = minCost(2 * node, start, (start + end) / 2, left, right);
		int rightMinCost = minCost(2 * node + 1, (start + end) / 2 + 1, end, left, right);
		
		return Math.min(leftMinCost, rightMinCost);
	}
	
	private static void initPsSegtree(int node, int start, int end) {
		if(start == end)
			psSegtree[node] = arr[start];
		else {
			//	left-subtree init
			initPsSegtree(2 * node, start, (start + end) / 2);
			//	right-subtree init
			initPsSegtree(2 * node + 1, (start + end) / 2 + 1, end);
			
			psSegtree[node] = psSegtree[2 * node] + psSegtree[2 * node + 1];
		}
	}
	
	private static long partSum(int node, int start, int end, int left, int right) {
		if(right < start || left > end)
			return 0;
		
		if(left <= start && end <= right)
			return psSegtree[node];
		
		//	left-subtree에 해당하는 부분합 구해서 반환하기
		long leftPartSum = partSum(2 * node, start, (start + end) / 2, left, right);
		//	right-subtree에 해당하는 부분합 구해서 반환하기
		long rightPartSum = partSum(2 * node + 1, (start + end) / 2 + 1, end, left, right);
	
		return leftPartSum + rightPartSum;
	}
	
	private static void update(int node, int start, int end, int idx, int value) {
		if(idx < start || idx > end)
			return;
		
		if(start == end) {
			arr[start] = value;
			psSegtree[node] = value;
			minSegtree[node] = value;
			return;
		}
		
		//	left-subtree에서 idx 찾아서 값 갱신하기
		update(2 * node, start, (start + end) / 2, idx, value);
		//	right-subtree에서 idx 찾아서 값 갱신하기
		update(2 * node + 1, (start + end) / 2 + 1, end, idx, value);
		
		psSegtree[node] = psSegtree[2 * node] + psSegtree[2 * node + 1];
		minSegtree[node] = Math.min(minSegtree[2 * node], minSegtree[2 * node + 1]);
	}
}	//	Main-class-end
```