# 25_10_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 6549 히스토그램에서 가장 큰 사각형

[6549번: 히스토그램에서 가장 큰 직사각형](http://boj.ma/6549/t)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Deque;
import java.util.ArrayDeque;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	
	private static int[] heights;	//	각 x 좌표에서의 직사각형 높이
	private static final Deque<Integer> stack = new ArrayDeque<>();
	
	private static long answer = 0;

	public static void main(String[] args) throws IOException {
		while (true) {
			st = new StringTokenizer(br.readLine());
			N = Integer.parseInt(st.nextToken());

			if (N == 0) {
				System.out.println(sb);
				break;
			}
			
			heights = new int[N];
			answer = 0;
			stack.clear();
			
			for(int x = 0; x < N; x++)
				heights[x] = Integer.parseInt(st.nextToken());
			
			for(int x = 0; x < N; x++) {
				int nowH = heights[x];
				
				while(!stack.isEmpty() && heights[stack.peekLast()] > nowH) {
					int prevX = stack.pollLast();
					int prevH = heights[prevX];
					int width = stack.isEmpty() ? x : x - stack.peekLast() - 1;
					
					answer = Math.max(answer, (long)prevH * width);
				}
				
				stack.offerLast(x);
			}
			
			while(!stack.isEmpty()) {
				int prevX = stack.pollLast();
				int prevH = heights[prevX];
				int width = stack.isEmpty() ? N : N - stack.peekLast() - 1;
				
				answer = Math.max(answer, (long)prevH * width);
			}
			
			sb.append(answer).append("\n");
		}
	} // main-end
} // Main-class-end
```

### BOJ 11873 최대 직사각형

[11873번: 최대 직사각형](http://boj.ma/11873/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int M;
	
	private static int[][] mat;
	private static int[] heights;	//	각 x 좌표에서의 직사각형 높이
	
	private static int answer = 0;

	public static void main(String[] args) throws IOException {
		while (true) {
			st = new StringTokenizer(br.readLine());
			N = Integer.parseInt(st.nextToken());
			M = Integer.parseInt(st.nextToken());

			if (N == 0 && M == 0) {
				System.out.println(sb);
				break;
			}
			
			mat = new int[N][M];
			heights = new int[M];
			answer = 0;
			
			for(int y = 0; y < N; y++) {
				st = new StringTokenizer(br.readLine());
				for(int x = 0; x < M; x++)
					mat[y][x] = Integer.parseInt(st.nextToken());
			}
			
			for(int y = 0; y < N; y++) {
				for(int x = 0; x < M; x++) {
					if(mat[y][x] == 1)	//	직사각형일 경우
						heights[x]++;	//	x에서의 높이 누적
					else	//	단절될 경우
						heights[x] = 0;
				}
				
				Deque<Integer> stack = new ArrayDeque<>();
				
				for(int x = 0; x < M; x++) {
					int height = heights[x];
					
					while(!stack.isEmpty() && heights[stack.peekLast()] > height) {
						int prevX = stack.pollLast();
						int prevH = heights[prevX];
						int width = stack.isEmpty() ? x : x - stack.peekLast() - 1;
						
						answer = Math.max(answer, prevH * width);
					}
					
					stack.offerLast(x);
				}
				
				while (!stack.isEmpty()) {
		            int h = heights[stack.pollLast()];
		            int width = stack.isEmpty() ? M : M - stack.peekLast() - 1;
		            answer = Math.max(answer, h * width);
		        }
			}
			
			sb.append(answer).append("\n");
		}
	} // main-end
} // Main-class-end
```