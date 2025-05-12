# 25_05_12_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 23818 원수의 원수

[23818번: 원수의 원수](http://boj.ma/23818/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;
	
	private static int N;	//	정점 개수
	private static int M;	//	간선 개수
	private static int K;	//	쿼리 개수
	
	private static List<int[]>[] graph;
	
	private static int ccNum;		//	connected-component 개수
	private static int[] ccNums;	//	connected-component 번호
	private static int[] teams;
	
	//	Key : cc 번호, Value : 모순이 있으면 true, 없으면 false
	private static final Map<Integer, Boolean> map = new HashMap<>();
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		
		graph = new ArrayList[N + 1];
		ccNums = new int[N + 1];
		teams = new int[N + 1];
		
		ccNum = 0;
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		for(int e = 0; e < M; e++) {
			st = new StringTokenizer(br.readLine());
			int t = Integer.parseInt(st.nextToken());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			graph[v1].add(new int[] {v2, t});
			graph[v2].add(new int[] {v1, t});
		}
		
		for(int v = 1; v <= N; v++) {
			if(ccNums[v] == 0) {
				ccNum++;
				dfs(v, ccNum, 1);
			}
		}
		
		for(int q = 0; q < K; q++) {
			st = new StringTokenizer(br.readLine());
			int v1 = Integer.parseInt(st.nextToken());
			int v2 = Integer.parseInt(st.nextToken());
			
			int cc1 = ccNums[v1];
			int cc2 = ccNums[v2];
			
			if(cc1 != cc2)	//	서로 다른 cc일 경우 아무 관계도 없음
				sb.append("Unknown\n");
			else {	//	서로 같은 cc일 경우
				if(map.getOrDefault(cc1, false))	//	모순이 존재할 경우
					sb.append("Error\n");
				else	//	모순이 없을 경우
					sb.append(teams[v1] == teams[v2] ? "Friend\n" : "Enemy\n");
			}
		}
		
		System.out.print(sb);
	} //	main-end
	
	private static void dfs(int v, int num, int team) {
		ccNums[v] = num;
		teams[v] = team;
		
		for(int[] edge : graph[v]) {
			int nv = edge[0];
			int t = edge[1];
			
			if(ccNums[nv] == 0) {	//	아직 방문하지 않은 정점
				if(t == 1)		//	원수 관계일 경우
					dfs(nv, num, 1 - team);
				else	//	친구 관계일 경우
					dfs(nv, num, team);
			}
			else {	//	이미 방문한 정점
				if(t == 1) {	//	원수 관계일 경우
					if(teams[nv] == teams[v])	//	원수 관계여야 하는데 같은 팀으로 된 경우
						map.put(num, true);	//	num번째 cc에는 모순이 존재
				}
				else {	//	친구 관계일 경우
					if(teams[nv] != teams[v])	//	친구 관계여야 하는데 다른 팀으로 된 경우
						map.put(num, true);	//	num번째 cc에는 모순이 존재
				}
			}
		}
	}
} //	Main-class-end
```