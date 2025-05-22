# 25_05_23_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 17038 The Great Revegetation (Silver)

[17038번: The Great Revegetation (Silver)](http://boj.ma/17038/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	private static List<int[]>[] graph;
	private static int[] color;
	private static boolean isValid = true;
	private static int components = 0;

	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		graph = new ArrayList[N + 1];
		color = new int[N + 1];
		Arrays.fill(color, -1); // -1: unvisited

		for (int i = 1; i <= N; i++)
			graph[i] = new ArrayList<>();

		for (int i = 0; i < M; i++) {
			st = new StringTokenizer(br.readLine());
			char type = st.nextToken().charAt(0);
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());

			int isSame = (type == 'S') ? 1 : 0;
			graph[v1].add(new int[] {v2, isSame});
			graph[v2].add(new int[] {v1, isSame});
		}

		for (int i = 1; i <= N; i++) {
			if (color[i] == -1) {
				components++;
				dfs(i, 0);
			}
		}

		if (!isValid)
			System.out.println(0);
		else {
			sb.append("1");
			for (int i = 0; i < components; i++) sb.append("0");
			System.out.println(sb);
		}
	}	//	main-end

	private static void dfs(int v, int c) {
		color[v] = c;
		for (int[] edge : graph[v]) {
			int next = edge[0];
			int expectedColor = edge[1] == 1 ?  c : 1 - c;

			if (color[next] == -1)
				dfs(next, expectedColor);
			else if (color[next] != expectedColor)
				isValid = false;
		}
	}
}	//	Main-class-end
```