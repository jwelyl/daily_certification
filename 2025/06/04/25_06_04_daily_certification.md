# 25_06_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 20119 클레어와 물약

[20119번: 클레어와 물약](http://boj.ma/20119/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.StringTokenizer;

public class Main {
	private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
	private static final StringBuilder sb = new StringBuilder();
	private static StringTokenizer st;

	private static int N;
	private static int M;
	private static int L;

	private static List<Integer>[] graph;
	private static boolean[] visited;
	private static int[] indegrees;		//	indegrees[i] : i번째 레시피에 필요한 물약 개수
	private static int[] recipes;		//	recipes[i] : i번째 레시피로 만드는 물약
	
	private static final Queue<Integer> queue = new LinkedList<>();
	
	private static int answer = 0;
	
	public static void main(String[] args) throws IOException {
		st = new StringTokenizer(br.readLine());
		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());

		graph = new ArrayList[N + 1];
		visited = new boolean[N + 1];
		indegrees = new int[M];
		recipes = new int[M];
		
		for(int v = 0; v <= N; v++)
			graph[v] = new ArrayList<>();
		
		for(int i = 0; i < M; i++) {
			st = new StringTokenizer(br.readLine());
			int k = Integer.parseInt(st.nextToken());
			
			indegrees[i] = k;	//	i번째 레시피를 위해 필요한 물약
			for(int j = 0; j < k; j++) {
				int v = Integer.parseInt(st.nextToken());
				graph[v].add(i);	//	v 물약은 i번째 레시피에 사용됨
			}
			recipes[i] = Integer.parseInt(st.nextToken());	//	i번째 레시피의 결과 물약
		}
		
		L = Integer.parseInt(br.readLine());
		st = new StringTokenizer(br.readLine());
		for(int l = 0; l < L; l++) {
			int cur = Integer.parseInt(st.nextToken());	//	cur 물약은 이미 완성됨
			visited[cur] = true;
			queue.offer(cur);
		}
		
		topologicalSort();

		for(int v = 1; v <= N; v++) {
			if(visited[v]) {
				answer++;
				sb.append(v).append(" ");
			}
		}
		
		System.out.println(answer);
		System.out.println(sb);
		
	} // main-end
	
	private static void topologicalSort() {
		while(!queue.isEmpty()) {
			int cur = queue.poll();	//	cur 물약은 이미 완성됨
			
			for(int recipe : graph[cur]) {	//	cur 물약이 사용되는 레시피
				indegrees[recipe]--;	//	레시피에 필요한 물약 개수 1 감소
				
				if(indegrees[recipe] == 0 && !visited[recipes[recipe]]) {	//	해당 레시피가 완성된 경우
					visited[recipes[recipe]] = true;	//	레시피로 완성된 물약 사용 가능
					queue.offer(recipes[recipe]);
				}
			}
		}
	}
} // Main-class-end
```

### 프로그래머스 등산코스 정하기

[](https://school.programmers.co.kr/learn/courses/30/lessons/118669)

```java
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;
import java.util.Arrays;

class Solution {
    private final long INF = Long.MAX_VALUE;
    private final int G = -1;
    private final int E = 0;
    private final int S = 1;
    
    private int N;
    
    private int[] GATES;
    private int[] SUMMITS;
    
    private List<int[]>[] graph;
    private int[] vertices;
    
    private static int maxIntensity = 0;
    
    public int[] solution(int n, int[][] paths, int[] gates, int[] summits) {
        int[] answer = {};
        
        N = n;
        vertices = new int[N + 1];
        
        GATES = gates;
        Arrays.sort(summits);
        SUMMITS = summits;
        
        for(int g : gates)
            vertices[g] = G;
        for(int s : summits)
            vertices[s] = S;
        
        graph = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        for(int[] edge : paths) {
            int v1 = edge[0];
            int v2 = edge[1];
            int c = edge[2];
            
            maxIntensity = Math.max(maxIntensity, c);
            
            if(vertices[v1] == E && vertices[v2] == E) {
                graph[v1].add(new int[] {v2, c});
                graph[v2].add(new int[] {v1, c});
            }
            else if(vertices[v1] == E) {
                if(vertices[v2] == G)
                    graph[v2].add(new int[] {v1, c});
                else
                    graph[v1].add(new int[] {v2, c});
            }
            else if(vertices[v2] == E) { 
                if(vertices[v1] == S)
                    graph[v2].add(new int[] {v1, c});
                else
                    graph[v1].add(new int[] {v2, c});
            }
            else {
                if(vertices[v1] != vertices[v2]) {
                    if(vertices[v1] == S)
                        graph[v2].add(new int[] {v1, c});
                    else
                        graph[v1].add(new int[] {v2, c});
                }
            }
        }

        return parametricSearch();
    }
    
    private long[] dijkstra(int limit) {
        PriorityQueue<long[]> pq = new PriorityQueue<>((n1, n2) -> Long.compare(n1[1], n2[1]));
        long[] dist = new long[N + 1];
        Arrays.fill(dist, INF);
        
        for(int g : GATES) {
            dist[g] = 0;
            pq.offer(new long[] {g, dist[g]});
        }
        
        while(!pq.isEmpty()) {
            long[] cur = pq.poll();
            int cv = (int)cur[0];
            long cc = cur[1];
            
            if(dist[cv] < cc)
                continue;
            
            for(int[] edge : graph[cv]) {
                if(edge[1] > limit)
                    continue;
                
                int nv = edge[0];
                long nc = cc + edge[1];
                
                if(nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new long[] {nv, dist[nv]});
                }
            }
        }
        
        return dist;
    }
    
    private int[] parametricSearch() {
        int start = 1;
        int end = maxIntensity;
        
        int[] res = {0, 0};
        
        while(start <= end) {
            int mid = (start + end) / 2;
            long[] dist = dijkstra(mid);

            boolean find = false;
            for(int s : SUMMITS) {
                if(dist[s] != INF) {
                    res[0] = s;
                    res[1] = mid;
                    find = true;
                    break;
                }
            }
            
            if(find)
                end = mid - 1;
            else
                start = mid + 1;
        }
    
        return res;
    }
}
```