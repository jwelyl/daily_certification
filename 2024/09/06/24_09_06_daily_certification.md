# 24_09_06_daily_certification

# Problem Solving (Algorithm & SQL)

### BOJ 27617 **Road To Savings**

[](https://www.acmicpc.net/problem/27617)

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
    private static final int INF = Integer.MAX_VALUE;
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static StringTokenizer st;

    private static int N;
    private static int M;
    private static int A;
    private static int B;
    
    private static List<Node>[] graph;
    private static List<Node>[] reversedGraph;
    private static int[] dist;

    private static int sum = 0;
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        A = Integer.parseInt(st.nextToken());
        B = Integer.parseInt(st.nextToken());

        graph = new ArrayList[N + 1];
        reversedGraph = new ArrayList[N + 1];
        for (int i = 0; i <= N; i++) {
        	graph[i] = new ArrayList<>();
        	reversedGraph[i] = new ArrayList<>();
        }
        dist = new int[N + 1];

        for (int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());

            sum += c;

            graph[v1].add(new Node(v2, c));
            graph[v2].add(new Node(v1, c));
        }

        dijkstra();

        System.out.println(sum - dfs(B));
    }
    
    private static int dfs(int v) {
    	int ret = 0;
    	
    	for(Node edge : reversedGraph[v]) {
    		ret += edge.cost;
    		ret += dfs(edge.vertex);
    	}
    		
    	return ret;
    }

    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();

        Arrays.fill(dist, INF);

        dist[A] = 0;
        pq.offer(new Node(A, dist[A]));

        while (!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv = cur.vertex;
            int cc = cur.cost;

            if (dist[cv] < cc) continue;

            for (Node next : graph[cv]) {
                int nv = next.vertex;
                int nc = cc + next.cost;

                if (nc < dist[nv]) {
                    dist[nv] = nc;
                    pq.offer(new Node(nv, dist[nv]));
                    reversedGraph[nv].clear();
                    reversedGraph[nv].add(new Node(cv, next.cost));
                }
                else if(nc == dist[nv])
                	reversedGraph[nv].add(new Node(cv, next.cost));
            }
        }
    }

    private static class Node implements Comparable<Node> {
        private int vertex;
        private int cost;
        
        public Node(int vertex, int cost) {
            this.vertex = vertex;
            this.cost = cost;
        }

        @Override
        public int compareTo(Node other) {
            return Integer.compare(this.cost, other.cost);
        }
    }
}
```