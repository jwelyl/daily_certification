# 24_12_22_daily_certification

### **BOJ 24042 횡단보도**

[24042번: 횡단보도](https://boj.ma/24042/t)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.PriorityQueue;

public class Main {
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;
    
    private static int N;
    private static int M;
    
    private static List<int[]>[] graph;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        for(int v = 0; v <= N; v++)
            graph[v] = new ArrayList<>();
        
        for(int e = 0; e < M; e++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new int[] {e, v2});
            graph[v2].add(new int[] {e, v1});
        }
        
        System.out.println(dijkstra());
    }    //    main-end
    
    private static long dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        long[] dist = new long[N + 1];
        
        Arrays.fill(dist, Long.MAX_VALUE);
        dist[1] = 0l;
        pq.offer(new Node(1, dist[1]));
        
        while(!pq.isEmpty()) {
        	Node cur = pq.poll();
        	int cv = cur.vertex;
        	long cc = cur.cost;
        	
        	if(dist[cv] < cc)
        		continue;
        	
        	for(int[] edge : graph[cv]) {
        		int ne = edge[0];	//	cv와 nv를 연결하는 횡단보도를 최초로 건널 수 있는 시간
        		int nv = edge[1];
        		long nc = Long.MAX_VALUE;
        		
        		//	횡단보도는 ne, ne + M, ne + 2 * M, ..., ne + k * M에 건널 수 있음
        		//	ne + k * M >= cc를 만족하는 가장 작은 k 찾아야 함
        		long k = Math.max(0, (long)Math.ceil((double)(cc - ne) / M));
        		nc = ne + k * M + 1;	//	ne + k * M에 거너면 nv에는 ne + k * M + 1에 도착함
        		
        		if(nc < dist[nv]) {
        			dist[nv] = nc;
        			pq.offer(new Node(nv, dist[nv]));
        		}
        	}
        }
        
        return dist[N];
    }
    
    private static class Node implements Comparable<Node> {
    	int vertex;
    	long cost;
    	
    	public Node(int vertex, long cost) {
    		this.vertex = vertex;
    		this.cost = cost;
    	}
    	
    	@Override
    	public int compareTo(Node other) {
    		return Long.compare(this.cost, other.cost);
    	}
    }
}    //    Main-class-end
```