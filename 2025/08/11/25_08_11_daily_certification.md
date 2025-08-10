# 25_08_11_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 9576 책 나눠주기

[9576번: 책 나눠주기](http://boj.ma/9576/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final int MAX = 1_000;
	private static final int NONE = -1;
	
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int T;
	private static int N;	//	책 개수
	private static int M;	//	사람 수
	
	private static final List<Integer>[] graph = new ArrayList[MAX + 1];
	private static final boolean[] visited = new boolean[MAX + 1];
	//	occupied[i] : i번째 책을 차지한 사람(없을 경우 NONE)
	private static final int[] occupied = new int[MAX + 1];
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		T = Integer.parseInt(br.readLine());
		
		for(int tc = 1; tc <= T; tc++) {
		init();
		
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		
		for(int person = 1; person <= M; person++) {
			st = new StringTokenizer(br.readLine());
			int a = Integer.parseInt(st.nextToken());
			int b = Integer.parseInt(st.nextToken());
			
			for(int book = a; book <= b; book++)
				graph[person].add(book);
		}
		
		for(int person = 1; person <= M; person++) {
			Arrays.fill(visited, false);
			
			if(dfs(person))
				answer++;
		}
			sb.append(answer).append("\n");
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static boolean dfs(int person) {
		for(int book : graph[person]) {
			if(!visited[book]) {
				visited[book] = true;
				
				int owner = occupied[book];	//	book을 차지한 사람
				
				if(owner == NONE || dfs(owner)) {	//	book을 차지한 사람이 없거나, 그 사람을 다른 책에 할당할 수 있을 경우
					occupied[book] = person;	//	book은 person이 차지함
					return true;	//	person이 책을 한 권 차지할 수 있음
				}
			}
		}
		
		return false;	//	person은 책을 차지할 수 없음
	}
	
	private static void init() {
		answer = 0;
		
		for(int v = 0; v <= MAX; v++) {
			graph[v] = new ArrayList<>();
			occupied[v] = NONE;
		}
	}
} //	Main-class-end
```