# 25_06_25_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 1385 벌집

[1385번: 벌집](http://boj.ma/1385/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_000_000;
	private static final int MAX_SIZE = 1_155;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static final int[][] map = new int[MAX_SIZE + 1][MAX_SIZE + 1];
	private static final int[][] pos = new int[MAX + 1][2];
	private static final int[] prev = new int[MAX + 1];
	
	private static int A;
	private static int B;
	
	public static void main(String[] args) throws IOException {
		drawMap();
		
        st = new StringTokenizer(br.readLine());
        A = Integer.parseInt(st.nextToken());
        B = Integer.parseInt(st.nextToken());
        
        if(A == B) {
        	System.out.println(A);
        	return;
        }
        
        bfs();
        path();
	} //	main-end
	
	private static void path() {
		Stack<Integer> pathStack = new Stack<>();
		int cur = B;
		
		while(true) {
			pathStack.push(cur);
			
			cur = prev[cur];
			if(cur == A) {
				pathStack.push(cur);
				break;
			}
		}
		
		while(!pathStack.isEmpty())
			sb.append(pathStack.pop()).append(" ");
		
		System.out.println(sb);
	}
	
	private static void bfs() {
		Queue<Integer> queue = new LinkedList<>();
		prev[A] = A;
		queue.offer(A);
		
		while(!queue.isEmpty()) {
			int cnum = queue.poll();
			int cy = pos[cnum][0];
			int cx = pos[cnum][1];
			
			for(int d = 0; d < 6; d++) {
				int ny = cy + dy[d];
				int nx = cx + dx[d];
				
				if(isIn(ny, nx)) {
					int nnum = map[ny][nx];
					
					if(nnum != 0 && prev[nnum] == 0) {
						prev[nnum] = cnum;
						if(nnum == B)
							return;
						
						queue.offer(nnum);
					}
				}
			}
		}
		
	}
	
	private static void drawMap() {
		Queue<int[]> queue = new LinkedList<>();
		map[MAX_SIZE / 2][MAX_SIZE / 2] = 1;
		pos[1][0] = MAX_SIZE / 2;
		pos[1][1] = MAX_SIZE / 2;
		queue.offer(new int[] {pos[1][0], pos[1][1]});
		
		int round = 0;
		int nextNum = 2;
		
		while(true) {
			int size = round == 0 ? 1 : 6 * round;
			
			int lastY = -1;
			int lastX = -1;
			
			for(int i = 0; i < size; i++) {
				int[] cur = queue.poll();
				int cy = cur[0];
				int cx = cur[1];
				
				for(int d = 0; d < 6; d++) {
					int ny = cy + dy[d];
					int nx = cx + dx[d];
					
					if(map[ny][nx] == 0) {
						map[ny][nx] = nextNum;
						pos[nextNum][0] = ny;
						pos[nextNum][1] = nx;
						lastY = ny;
						lastX = nx;
						
						if(nextNum == MAX)
							return;
						
						queue.offer(new int[] {pos[nextNum][0], pos[nextNum][1]});
						
						nextNum++;
					}
				}
			}
			
			lastY--;
			map[lastY][lastX] = nextNum;
			pos[nextNum][0] = lastY;
			pos[nextNum][1] = lastX;
			queue.offer(new int[] {lastY, lastX});
			nextNum++;
			
			round++;
		}
	}
	
	private static final int[] dy = {-1, 0, 1, 1, 0, -1};
	private static final int[] dx = {0, 1, 1, 0, -1, -1};
	
	private static boolean isIn(int y, int x) {
		return (0 <= y && y <= MAX_SIZE) && (0 <= x && x <= MAX_SIZE);
	}
} //	Main-class-end
```

### BOJ 2138 전구와 스위치

[2138번: 전구와 스위치](http://boj.ma/2138/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;

public class Main {
	private static final int NONE = Integer.MAX_VALUE;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

	private static int N;
	
	//	cur1 : 처음 주어진 상태 그대로
	private static char[] cur1;
	//	cur2 : 처음 주어진 상태에서 첫번째 스위치 누른 상태
	private static char[] cur2;
	private static char[] target;
	
	private static int answer1 = 0;
	private static int answer2 = 0;
	private static int answer = NONE;

	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		cur1 = br.readLine().toCharArray();
		cur2 = Arrays.copyOf(cur1, N);
		target = br.readLine().toCharArray();
		
		cur2[0] = cur2[0] == '1' ? '0' : '1';
		cur2[1] = cur2[1] == '1' ? '0' : '1';
		answer2++;
		
		for(int i = 1; i < N; i++) {
			if(target[i - 1] != cur1[i - 1]) {
				answer1++;
				flip(i, cur1);
			}
			if(target[i - 1] != cur2[i - 1]) {
				answer2++;
				flip(i, cur2);
			}
		}
		
		if(target[N - 1] != cur1[N - 1])
			answer1 = NONE;
		if(target[N - 1] != cur2[N - 1])
			answer2 = NONE;
		
		answer = Math.min(answer1, answer2);
		
		System.out.println(answer == NONE ? -1 : answer);
	}	// main-end
	
	private static void flip(int idx, char[] cur) {
		cur[idx - 1] = cur[idx - 1] == '1' ? '0' : '1';
		cur[idx] = cur[idx] == '1' ? '0' : '1';
		
		if(idx + 1 < N)
			cur[idx + 1] = cur[idx + 1] == '1' ? '0' : '1';
	}
}	//	Main-class-end
```