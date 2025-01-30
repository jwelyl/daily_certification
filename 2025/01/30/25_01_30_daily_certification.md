# 25_01_30_daily_certification

# Problem Solving (Algorithm & SQL)

### **BOJ 1777 순열복원**

[1777번: 순열복원](http://boj.ma/1777/t)

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
	private static int[] inverse;
	private static int[] segtree;
	private static int[] res;
	
	private static int H;
	private static int size;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());

		inverse = new int[N + 1];
		res = new int[N + 1];
		
		H = (int)Math.ceil(Math.log(N) / Math.log(2)) + 1;
		size = 1 << H;
		
		segtree = new int[size];
		init(segtree, 1, 1, N);

		st = new StringTokenizer(br.readLine());
		for(int i = 1; i <= N; i++)
			inverse[i] = Integer.parseInt(st.nextToken());
		
		for (int i = N; i >= 1; i--) {
			int x = query(segtree, 1, 1, N, inverse[i]);
			res[x] = i;
			update(segtree, 1, 1, N, x);
		}

		for (int i = 1; i <= N; i++) 
			sb.append(res[i]).append(" ");
		
		System.out.println(sb);
	}	//	main-end

	private static void init(int[] segtree, int node, int start, int end) {
		if (start == end) {
			segtree[node] = 1;
			return;
		}

		init(segtree, 2 * node, start, (start + end) / 2);
		init(segtree, 2 * node + 1, (start + end) / 2 + 1, end);
		segtree[node] = segtree[node * 2] + segtree[node * 2 + 1];
	}

	private static void update(int[] segtree, int node, int start, int end, int idx) {
		if (idx < start || end < idx)
			return;
		
		if (start == end) {
			segtree[node] = 0;
			return;
		}
		
		update(segtree, 2 * node, start, (start + end) / 2, idx);
		update(segtree, 2 * node + 1, (start + end) / 2 + 1, end, idx);
		segtree[node] -= 1;
	}

	private static int query(int[] segtree, int node, int start, int end, int val) {
		if (start == end)
			return start;

		if (segtree[2 * node+ 1] > val)
			return query(segtree, 2 * node + 1, (start + end) / 2 + 1, end, val);
		else
			return query(segtree, 2 * node, start, (start + end) / 2, val - segtree[2 * node + 1]);
	}
}	//	Main-class-end
```