# 25_04_29_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 2610 회의준비

[2610번: 회의준비](http://boj.ma/2610/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
	
	private static int N;
	private static int M;
	
	private static boolean[] visited;
	private static List<Integer>[] graph;								//	적대 관계 그래프
	private static final List<int[]> friendship = new ArrayList<>();	//	친구 관계
	
	private static int[] parents;
	
	private static int teamCnt = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		M = Integer.parseInt(br.readLine());
		
		graph = new ArrayList[N + 1];
		teamCnt = N;
		visited =new boolean[N + 1];
		parents = new int[N + 1];
		for(int v = 1; v <= N; v++) {
			graph[v] = new ArrayList<>();
			parents[v] = v;
		}
		
		for(int m = 0; m < M; m++) {
			st = new StringTokenizer(br.readLine());
			char r = st.nextToken().charAt(0);
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			if(r == 'E') {	//	적대 관계일 경우
				graph[v1].add(v2);
				graph[v2].add(v1);
			}
			else
				friendship.add(new int[] {v1, v2});
		}
		
		for(int v = 1; v <= N; v++) {
			if(!visited[v]) {
				List<Integer>[] teams = new ArrayList[2];
				teams[0] = new ArrayList<>();
				teams[1] = new ArrayList<>();
				
				dfs(v, 0, teams);
				
				union(teams[0]);
				union(teams[1]);
			}
		}
		
		for(int[] r : friendship) {
			int v1 = r[0];
			int v2 = r[1];
			
			union(v1, v2);
		}
		
		System.out.println(teamCnt);
	} //	main-end
	
	private static void dfs(int v, int teamIdx, List<Integer>[] teams) {
		visited[v] = true;
		teams[teamIdx].add(v);
		
		for(int nv : graph[v]) {
			if(!visited[nv])
				dfs(nv, 1 - teamIdx, teams);
		}
	}
	
	private static int find(int v) {
		if(v == parents[v])
			return v;
		
		return parents[v] = find(parents[v]);
	}
	
	private static void union(int v1, int v2) {
		v1 = find(v1);
		v2 = find(v2);
		
		if(v1 != v2) {
			teamCnt--;
			parents[v2] = v1;
		}
	}
	
	private static void union(List<Integer> team) {
		if(team.size() >= 2) {
			for(int i = 1; i < team.size(); i++) {
				int v1 = team.get(i - 1);
				int v2 = team.get(i);
				
				union(v1, v2);
			}
		}
	}
} //	Main-class-end
```