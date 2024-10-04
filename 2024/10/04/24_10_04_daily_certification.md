# 24_10_04_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 13397 구간 나누기 2

[](https://www.acmicpc.net/problem/13397)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    N개의 자연수
    private static int M;    //    M개 이하 구간으로 나눌 수 있음
    private static int[] nums;
    
    private static int min = 10_001;    //    N개 수 중 최솟값
    private static int max = -1;        //    N개 수 중 최댓값
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        nums = new int[N];
        
        st = new StringTokenizer(br.readLine());
        for(int i = 0; i < N; i++) {
            nums[i] = Integer.parseInt(st.nextToken());
            min = Math.min(min, nums[i]);
            max = Math.max(max, nums[i]);
        }
        
        System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;            //    구간에서 (최댓값 - 최솟값)의 이론상 최솟값
        int end = max - min;      //    구간에서 (최댓값 - 최솟값)의 이론상 최댓값
        int res = 0;
        
        while(start <= end) {
            int mid = (start + end) / 2;    //    구간에서 (최댓값 - 최솟값)을 mid 이하로 유지할 때
            int cnt = cntOf(mid);
            
            if(cnt <= M) {    //    그 때 구간 개수가 M개 이하면
                res = mid;        //    일단 그 값 저장
                end = mid - 1;    //    mid 값을 줄여보기
            } 
            else                     //    구간 개수를 M개 이하로 맞추지 못할 경우
                start = mid + 1;     //    mid 값을 늘려서 구간 길이를 더 키워보기
        }
        
        return res;
    }
    
    //    최댓값 - 최솟값이 maxDiff 이하가 되도록 구간을 나눌 때 필요한 구간 개수
    private static int cntOf(int maxDiff) {
        int cnt = 0;    //    구간 개수
        
        int start = 0;
        int end = start;
        int min = nums[end];
        int max = nums[end];
        int diff = max - min;
        
        while(start <= end && end < N) {
        	if(diff <= maxDiff) {		//	[start, end]가 가능할 경우
        		if(end + 1 == N) {	//	끝까지 온 경우 (구간을 더 늘려볼 수 없을 경우)
        			cnt++;
        			break;
        		}
        		
        		end++;	//	구간 더 늘려보기
        		min = Math.min(min, nums[end]);
            	max = Math.max(max, nums[end]);
            	diff = max - min;
        	}
        	else {	//	구간을 더 늘릴 수 없는 경우
        		cnt++;			//	[start, end - 1]은 가능
        		start = end;	
        		end = start;	//	end를 start로 다시 시작
        		
        		min = nums[end];
        		max = nums[end];
        		diff = max - min;
        	}        	
        }

        return cnt;
    }
}    //    Main-class-end
```

### BOJ 1800 인터넷 설치

[](https://www.acmicpc.net/problem/1800)

**BFS**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int P;    //    간선 개수
    private static int K;    //    공짜 간선 개수
    
    private static List<Node>[] graph;    //    그래프
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        P = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int i = 1; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        for(int p = 1; p <= P; p++) {
            st = new StringTokenizer(br.readLine());
        
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Node(v2, c));
            graph[v2].add(new Node(v1, c));
        }
        
        if(N == 1)
            System.out.println(0);
        else
            System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = 1_000_000;
        int res = -1;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(bfs(mid)) {    //    mid보다 큰 간선을 K개 이하로 이용해서 1부터 N까지 도달할 수 있으면
                res = mid;
                end = mid - 1;        //    mid를 더 줄여보기
            }
            else                      //    mid보다 큰 간선 개수가 K개보다 더 필요할 경우
                start = mid + 1;      //    mid를 키워보기
        }
        
        return res;
    }
    
    private static boolean bfs(int cost) {
        Queue<int[]> q = new LinkedList<>();
        boolean[][] visited = new boolean[N + 1][K + 1];
        
        visited[1][K] = true;
        q.offer(new int[] {1, K});
        
        while(!q.isEmpty()) {
            int[] cur = q.poll();
            int cv = cur[0];
            int ccnt = cur[1];    //    cost보다 더 비싼 간선 쓸 수 있는 횟수
            
            for(Node next : graph[cv]) {
                int nv = next.vertex;
                int nc = next.cost;
                
                if(nc > cost) {    //    cv에서 nv로 공짜 간선 1개 써야 할 경우
                    if(ccnt >= 1) {    //    cv에서 nv로 갈 수 있을 경우
                        if(nv == N)
                            return true;
                        
                        if(!visited[nv][ccnt - 1]) {
                            visited[nv][ccnt - 1] = true;
                            q.offer(new int[] {nv, ccnt - 1});
                        }
                    }
                }
                else {    //    cv에서 nv로 그냥 갈 수 있을 경우
                    if(nv == N)
                        return true;
                    
                    if(!visited[nv][ccnt]) {
                        visited[nv][ccnt] = true;
                        q.offer(new int[] {nv, ccnt});
                    }
                }
            }
        }    //    while-end
        
        return false;
    }
    
    private static class Node {
        public int vertex;
        public int cost;
        
        public Node(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
    }
}    //    Main-class-end
```

**Dijkstra**

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
	private static final int INF = 10_001;
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;    //    정점 개수
    private static int P;    //    간선 개수
    private static int K;    //    공짜 간선 개수
    
    private static List<Edge>[] graph;    //    그래프
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        
        N = Integer.parseInt(st.nextToken());
        P = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int i = 1; i <= N; i++)
            graph[i] = new ArrayList<>();
        
        for(int p = 1; p <= P; p++) {
            st = new StringTokenizer(br.readLine());
        
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Edge(v2, c));
            graph[v2].add(new Edge(v1, c));
        }
        
        if(N == 1)
            System.out.println(0);
        else
            System.out.println(parametricSearch());
    }    //    main-end
    
    private static int parametricSearch() {
        int start = 0;
        int end = 1_000_000;
        int res = -1;
        
        while(start <= end) {
            int mid = (start + end) / 2;
            
            if(dijkstra(mid) > K)	//	1에서 N까지 가는데 mid보다 큰 간선이 K개 넘게 필요할 경우
            	start = mid + 1;	//	mid 값을 키워야 함
            else {	//	1에서 N까지 가는데 mid보다 큰 간선이 K개 이하로 필요할 경우
            	res = mid;	//	일단 mid 값 저장
            	end = mid - 1;	//	mid 값 줄여보기
            }
        }
        
        return res;
    }
    
    private static int dijkstra(int cost) {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        int[] dist = new int[N + 1];	//	dist[i] : 1에서 i까지 가는데 필요한 비용이 cost보다 큰 간선 개수
        Arrays.fill(dist, INF);
        
        dist[1] = 0;
        pq.offer(new Node(1, dist[1]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cc = cur.cost;
            
            if(dist[cv] < cc)
            	continue;
            
            for(Edge next : graph[cv]) {
            	int nv = next.vertex;
            	int nc = next.cost > cost ? cc + 1 : cc;	//	next 간선 비용이 cost보다 크면 필요 개수 1 증가
            	
            	if(nc < dist[nv]) {
            		dist[nv] = nc;
            		pq.offer(new Node(nv, dist[nv]));
            	}
            }
        }    //    while-end
        
        return dist[N];
    }
    
    private static class Edge {
        public int vertex;
        public int cost;
        
        public Edge(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
    }
    
    private static class Node implements Comparable<Node> {
    	public int vertex;
        public int cost;
        
        public Node(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
        	return Integer.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```

### BOJ 2143 두 배열의 합

[](https://www.acmicpc.net/problem/2143)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Map;
import java.util.HashMap;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int T;
    private static int N;
    private static int M;
    
    private static int[] prefixSumA;
    private static int[] prefixSumB;
    
    private static final Map<Integer, Integer> partSumMap = new HashMap<>();
    
    private static long ans = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        T = Integer.parseInt(st.nextToken());
        
        N = Integer.parseInt(br.readLine());
        prefixSumA = new int[N + 1];
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= N; i++)
            prefixSumA[i] = prefixSumA[i - 1] + Integer.parseInt(st.nextToken());
        
        M = Integer.parseInt(br.readLine());
        prefixSumB = new int[M + 1];
        st = new StringTokenizer(br.readLine());
        for(int i = 1; i <= M; i++)
            prefixSumB[i] = prefixSumB[i - 1] + Integer.parseInt(st.nextToken());
        
        for(int i = 1; i <= N; i++) {
            for(int j = i; j <= N; j++) {
                int partSum = prefixSumA[j] - prefixSumA[i - 1];
                
                partSumMap.put(partSum, partSumMap.getOrDefault(partSum, 0) + 1);
            }
        }
        
        for(int i = 1; i <= M; i++) {
            for(int j = i; j <= M; j++) {
                int partSum = prefixSumB[j] - prefixSumB[i - 1];
                int cnt = partSumMap.getOrDefault(T - partSum, 0);
                
                ans += cnt;
            }
        }
        
        System.out.println(ans);
    }    //    main-end
}    //    Main-class-end
```