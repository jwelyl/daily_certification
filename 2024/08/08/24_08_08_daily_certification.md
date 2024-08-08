# 24_08_08_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 10090 **Counting Inversions**

[](https://www.acmicpc.net/problem/10090)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static int[] segtree;
	private static StringTokenizer st;

	private static int N;

	private static int[] nums;

	private static int H;
	private static int size;
	
	private static long ans;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		H = (int) Math.ceil(Math.log(N) / Math.log(2)) + 1;
		size = 1 << H;

		nums = new int[N + 1];
		segtree = new int[size];
		
		st = new StringTokenizer(br.readLine());

		for (int i = 1; i <= N; i++)
			nums[i] = Integer.parseInt(st.nextToken());

		for (int i = N; i >= 1; i--) {
			int num = nums[i];
			ans += query(1, N, 1, 1, num - 1);
			update(1, N, 1, num);
		}

		System.out.println(ans);
	} // main-end

	private static int update(int start, int end, int node, int idx) {
		if (end < idx || idx < start)
			return segtree[node];
		
		if (start == end)
			return segtree[node] += 1;

		return segtree[node] = update(start, (start + end) / 2, 2 *  node, idx) + update((start + end) / 2 + 1, end, 2 * node + 1, idx);
	}

	private static int query(int start, int end, int node, int left, int right) {
		if (end < left || right < start)
			return 0;
		
		if (left <= start && end <= right)
			return segtree[node];
		
		return query(start, (start + end) / 2, 2 * node, left, right) + query((start + end) / 2 + 1, end, 2 * node + 1, left, right);
	}
} // Main-class-end
```