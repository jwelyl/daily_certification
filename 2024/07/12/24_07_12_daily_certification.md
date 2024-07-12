# 24_07_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2042 구간 합 구하기

[](https://www.acmicpc.net/problem/2042)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static int N;	//	수 개수
	private static int M;	//	수 변경 횟수
	private static int K;	//	구간 합 구하는 횟수
	
	private static int Q;	//	쿼리 수
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		N = Integer.parseInt(tokens.nextToken());
		M = Integer.parseInt(tokens.nextToken());
		K = Integer.parseInt(tokens.nextToken());
		
		Q = M + K;
		
		long[] nums = new long[N];	//	구간합 구할 수 배열
		
		int H = (int)Math.ceil(Math.log(N) / Math.log(2)) + 1;
		int size = (1 << H);
		long[] segtree = new long[size];
		
		for(int i = 0; i < N; i++)
			nums[i] = Long.parseLong(br.readLine());
		
		init(nums, segtree, 1, 0, N - 1);
		
		for(int q = 0; q < Q; q++) {
			tokens = new StringTokenizer(br.readLine());
			int cmd = Integer.parseInt(tokens.nextToken());
			
			if(cmd == 1) {
				int idx = Integer.parseInt(tokens.nextToken()) - 1;
				long value = Long.parseLong(tokens.nextToken());
				
				update(nums, segtree, 1, 0, N - 1, idx, value);
			}
			else {
				int left = Integer.parseInt(tokens.nextToken()) - 1;
				int right = Integer.parseInt(tokens.nextToken()) - 1;
				sb.append(partSum(segtree, 1, 0, N - 1, left, right)).append("\n");
			}
		}
		
		System.out.print(sb);
	}	//	main-end
	
	private static void init(long[] nums, long[] segtree, int node, int start, int end) {
		if(start == end)
			segtree[node] = nums[start];
		else {
			//	left-subtree init
			init(nums, segtree, 2 * node, start, (start + end) / 2);
			//	right-subtree init
			init(nums, segtree, 2 * node + 1, (start + end) / 2 + 1, end);
			
			segtree[node] = segtree[2 * node] + segtree[2 * node + 1];
		}
	}
	
	//	segtree의 node번 노드가 담당하는 nums의 [start, end] 범위에서, nums의 [left, right] 부분합 구하기
	private static long partSum(long[] segtree, int node, int start, int end, int left, int right) {
		if(right < start || left > end)	//	부분합 구하려는 범위가 node번 노드가 담당하는 범위를 벗어날 경우
			return 0;
		
		if(left <= start && end <= right)	//	부분합 구하려는 범위가 node번 노드가 담당하는 범위를 전부 포함할 경우
			return segtree[node];
		
		//	left subtree에 해당하는 부분합 구하기
		long leftPartSum = partSum(segtree, 2 * node, start, (start + end) / 2, left, right);
		//	right subtree에 해당하는 부분합 구하기
		long rightPartSum = partSum(segtree, 2 * node + 1, (start + end) / 2 + 1, end, left, right);
		
		return leftPartSum + rightPartSum;
	}
	
	//	nums의 idx번째 값을 value로 변경
	private static void update(long[] nums, long[] segtree, int node, int start, int end, int idx, long value) {
		if(idx < start || idx > end)	//	값을 변경하려는 위치가 node번 노드가 담당하는 범위를 벗어날 경우
			return;
		
		if(start == end) {	//	값을 변경하려는 위치가 단말 노드에 해당할 경우
			nums[idx] = value;
			segtree[node] = value;
			return;
		}
		
		//	left subtree에서 idx 찾아서 값 변경하기
		update(nums, segtree, 2 * node, start, (start + end) / 2, idx, value);
		//	right subtree에서 idx 찾아서 값 변경하기
		update(nums, segtree, 2 * node + 1, (start + end) / 2 + 1, end, idx , value);
		
		//	left, right subtree 변경된 것 현재 노드에 반영하기
		segtree[node] = segtree[2 * node] + segtree[2 * node + 1];
	}
}	//	Main-class-end
```

### BOJ 6213 **Balanced Lineup**

[](https://www.acmicpc.net/problem/6213)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer tokens;
	
	private static int N;			//	소 개수
	private static int Q;			//	쿼리 개수
	private static int[] heights;	//	소 길이
	private static int[] maxSegtree;	//	최댓값 세그먼트 트리 (maxSegtree[node] : node번 세그트리가 담당하는 범위의 소의 최대 길이)
	private static int[] minSegtree;	//	최솟값 세그먼트 트리 (minSegtree[node] : node번 세그트리가 담당하는 범위의 소의 최대 길이)
	
	private static int H;			//	세그먼트 트리 높이
	private static int size;		//	세그먼트 트리 크기
	
	public static void main(String[] args) throws IOException {
		tokens = new StringTokenizer(br.readLine());
		N = Integer.parseInt(tokens.nextToken());
		Q = Integer.parseInt(tokens.nextToken());
	
		heights = new int[N];
		H = (int)Math.ceil(Math.log(N) / Math.log(2)) + 1;
		size = (1 << H);
		
		for(int i = 0; i < N; i++)
			heights[i] = Integer.parseInt(br.readLine());
		
		maxSegtree = new int[size];
		minSegtree = new int[size];
		
		init(maxSegtree, heights, 1, 0, N - 1, true);
		init(minSegtree, heights, 1, 0, N - 1, false);
		
		for(int q = 0; q < Q; q++) {
			tokens = new StringTokenizer(br.readLine());
			int left = Integer.parseInt(tokens.nextToken()) - 1;
			int right = Integer.parseInt(tokens.nextToken()) - 1;
			
			int max = query(maxSegtree, 1, 0, N - 1, left, right, true);	//	[left, right]에서의 최대 길이
			int min = query(minSegtree, 1, 0, N - 1, left, right, false);	//	[left, right]에서의 최소 길이
			
			sb.append(max - min).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
	
	//	node가 담당하는 [start, end] 범위에서 최대/최소 길이 찾아서 segtree[node]에 저장
	//	max가 true일 경우 최대 길이, false일 경우 최소 길이 찾아서 segtree[node]에 저장
	private static void init(int[] segtree, int[] heights, int node, int start, int end, boolean max) {
		if(start == end)
			segtree[node] = heights[start];	//	단말 노드일 경우, 담당하는 소가 1마리임, 해당 소 길이가 최대/최소 길이임
		else {
			//	left subtree에서 최대/최소 길이 찾기
			init(segtree, heights, 2 * node, start, (start + end) / 2, max);
			//	right subtree에서 최대/최소 길이 찾기
			init(segtree, heights, 2 * node + 1, (start + end) / 2 + 1, end, max);
			
			//	left, right subtree에서 각각 최대/최소 길이 찾기 비교해서 최대/최소 길이 찾기
			segtree[node] = max ? (Math.max(segtree[2 * node], segtree[2 * node + 1])) : (Math.min(segtree[2 * node], segtree[2 * node + 1]));
		}
	}
	
	//	node가 담당하는 [start, end] 범위에서 [left, right] 범위 내의 최대/최소 길이 찾기
	private static int query(int[] segtree, int node, int start, int end, int left, int right, boolean max) {
		if(left > end || right < start)	//	node가 담당하는 범위 밖일 경우
			return max ? Integer.MIN_VALUE : Integer.MAX_VALUE;	//	최대 길이 찾을 경우 최솟값, 최소 길이 찾을 경우 최댓값
		
		if(left <= start && end <= right)	//	최대/최소 구하려는 범위가 node가 담당하는 범위를 전부 포함하는 경우
			return segtree[node];
		
		//	left subtree에서 최대/최소 길이 찾기
		int leftRes = query(segtree, 2 * node, start, (start + end) / 2, left, right, max);
		//	right subtree에서 최대/최소 길이 찾기
		int rightRes = query(segtree, 2 * node + 1, (start + end) / 2 + 1, end, left, right, max);
		
		return max ? Math.max(leftRes, rightRes) : Math.min(leftRes, rightRes);
	}
}	//	Main-class-end
```