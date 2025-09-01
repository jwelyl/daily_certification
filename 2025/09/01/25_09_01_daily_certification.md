# 25_09_01_daily_certification

# To Do List

```
[#244 koreii] 데일리인증 20250901
0. 역량검사
1. 서류 작성
2. 알고리즘 문제 풀이
```

```
[#063] 데일리인증 20250901 (244 / 365)
0. 역량검사
1. 서류 작성
2. 알고리즘 문제 풀이
- BOJ 1507 궁금한 민호 (Floyd Warshall)
```

# Problem Solving (Algorithm & SQL)

### BOJ 1507 궁금한 민호

[1507번: 궁금한 민호](http://boj.ma/1507/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static StringTokenizer st;
		
	private static int N;
	
	private static int[][] dp;		//	floyd-warshall 결과, dp[i][j] : 정점 i에서 j로 최단거리
	private static int[][] graph;	//	floyd-warshall 진행 전, graph[i][j] : 정점 i, j 간선 비용
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		N = Integer.parseInt(br.readLine());
		
		dp = new int[N + 1][N + 1];
		graph = new int[N + 1][N + 1];
		
		for(int i = 1; i <= N; i++) {
			st = new StringTokenizer(br.readLine());
			for(int j = 1; j <= N; j++) {
				dp[i][j] = Integer.parseInt(st.nextToken());
				graph[i][j] = dp[i][j]; 
			}
		}
		
		for(int k = 1; k <= N; k++) {
			for(int i = 1; i <= N; i++) {
				for(int j = 1; j <= N; j++) {
					if(i == j || j == k || k == i)
						continue;
					
					//	i에서 j의 최단거리보다 (i -> k 최단거리) + (k -> j 최단거리)가 작을 경우 모순
					if(dp[i][j] > dp[i][k] + dp[k][j]) {
						System.out.println(-1);
						return;
					}
					
					//	i에서 j의 최단거리는 k를 경유함
					//	i - j는 직접 연결되어 있지 않음
					if(dp[i][j] == dp[i][k] + dp[k][j])
						graph[i][j] = 0;
				}
			}
		}
		
		for(int i = 1; i < N; i++) {
			for(int j = i + 1; j <= N; j++)
				answer += graph[i][j];
		}
		
		System.out.println(answer);
	} //	main-end
} //	Main-class-end
```