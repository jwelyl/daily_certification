# 24_08_09_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2957 **이진 탐색 트리**

[](https://www.acmicpc.net/problem/2957)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.TreeSet;

public class Main {
	private static final int NONE = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	
	private static final TreeSet<Integer> bst = new TreeSet<>();
	
	private static int N;				//	노드 개수
	private static int[] depth;			//	depth[i] : i번째 노드의 깊이
	private static long count = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		depth = new int[N + 2];
		depth[0] = depth[N + 1] = NONE;
		
		bst.add(0);
		bst.add(N + 1);
		
		for(int i = 1; i <= N; i++) {
			int num = Integer.parseInt(br.readLine());
			
			depth[num] = Math.max(depth[bst.lower(num)], depth[bst.higher(num)]) + 1;
			count += depth[num];
			bst.add(num);
			sb.append(count).append("\n");
		}
		
		System.out.print(sb);
	}	//	main-end
}	//	Main-class-end
```