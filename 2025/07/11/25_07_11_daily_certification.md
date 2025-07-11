# 25_07_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 8112 0과 1 - 2

[8112번: 0과 1 - 2](http://boj.ma/8112/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	
	private static int T;
	private static int N;
	
	private static boolean[] visited;

	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
			N = Integer.parseInt(br.readLine());
			
			sb.append(bfs()).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static String bfs() {
		if(N == 1)
			return "1";
		
		Queue<Node> queue = new LinkedList<>();
		boolean[] visited = new boolean[N + 1];
		visited[1] = true;
		queue.offer(new Node(1, "1"));

		while(!queue.isEmpty()) {
			Node cur = queue.poll();
			int cnum = cur.num;
			String cstr = cur.str;
			
			for(int digit = 0; digit <= 1; digit++) {
				int nnum = (cnum * 10 + digit) % N;
				String nstr = cstr + (char)(digit + '0');
				
				if(nnum == 0)
					return nstr;
				
				if(!visited[nnum]) {
					visited[nnum] = true;
					queue.offer(new Node(nnum, nstr));
				}
			}
		}
		
		return "BRAK";
	}
	
	private static class Node {
		public int num;
		public String str;
		
		public Node(int num, String str) {
			this.num = num;
			this.str = str;
		}
	}
} //	Main-class-end
```