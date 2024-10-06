# 24_10_06_daily_certification

```
[#280 koreii] 데일리인증 20241006
1. 실전 코딩테스트 1개 + 토/일 코딩테스트 후기 작성
2. 서류 1개 작성 후 지원
3. 알고리즘 학습
- 1차원 Knapsack
4. 알고리즘 & 코딩 테스트 대비 (복습)
- TreeSet (21939 문제 추천 시스템 Version 1)
- Dijkstra, Shortest Path Tree (2211 네트워크 복구)
```

# Problem Solving (Algorithm & SQL)

### BOJ 21939 문제 추천 시스템 Version 1

[](https://www.acmicpc.net/problem/21939)

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
import java.util.TreeSet;

public class Main {
	private static final int MAX_ID = 100_001; 
	
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static final int[] idToRatingArr = new int[MAX_ID];
    private static final TreeSet<Problem> set = new TreeSet<>();    //    문제 저장
    
    private static int N;    //    초기 문제 수
    private static int M;    //    쿼리  수
    
    public static void main(String[] args) throws IOException {
        N = Integer.parseInt(br.readLine());
        
        for(int i = 1; i <= N; i++) {
            st = new StringTokenizer(br.readLine());
            int id = Integer.parseInt(st.nextToken());
            int rating = Integer.parseInt(st.nextToken());
            
            set.add(new Problem(id, rating));
            idToRatingArr[id] = rating;
        }
        
        M = Integer.parseInt(br.readLine());
        
        for(int q = 1; q <= M; q++) {
            st = new StringTokenizer(br.readLine());
            
            String option = st.nextToken();
            int p = 0;    //    문제 번호
            int l = 0;    //    문제 난이도
            Problem prob = null;
            
            switch(option) {
                case "recommend":
                    int x = Integer.parseInt(st.nextToken());
                    
                    if(x == 1) {    //    가장 어려운 문제, 여러 개일 경우 문제 번호가 가장 큰 문제의 문제 번호
                        prob = set.last();
                        sb.append(prob.id).append("\n");
                    }
                    else {    //    가장 쉬운 문제, 여러 개일 경우 문제 번호가 가장 작은 문제의 문제 번호
                        prob = set.first();
                        sb.append(prob.id).append("\n");
                    }
                    break;
                case "add":
                    p = Integer.parseInt(st.nextToken());
                    l = Integer.parseInt(st.nextToken());
                    
                    set.add(new Problem(p, l));
                    idToRatingArr[p] = l;
                    break;
                default:
                    p = Integer.parseInt(st.nextToken());    //    문제 번호
                    l = idToRatingArr[p];    //    해당 문제의 난이도
                    
                    set.remove(new Problem(p, l));    //    문제 번호가 p, 난이도가 l인 문제 제거
                    break;
            }
        }
        
        System.out.print(sb);
    }    //    main-end
    
    private static class Problem implements Comparable<Problem> {
        public int id;        //    문제 번호
        public int rating;    //    문제 난이도
        
        public Problem(int id, int rating) {
            this.id = id;
            this.rating = rating;
        }
        
        @Override
        public int compareTo(Problem other) {
            int res = Integer.compare(this.rating, other.rating);    //    문제 난이도가 쉬운 순으로
            
            if(res == 0)    //    난이도가 같을 경우
                res = Integer.compare(this.id, other.id);    //    문제 번호가 작은 순으로
            
            return res;
        }
    }
}    //    Main-class-end
```

### BOJ 2211 네트워크 복구

[](https://www.acmicpc.net/problem/2211)

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

public class Main {
    private static final int INF = 1_000 * 1_000 * 10 + 1;
    private static final int ROOT = 1;    //    슈퍼 컴퓨터
    
    private static final BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    private static final StringBuilder sb = new StringBuilder();
    private static StringTokenizer st;
    
    private static int N;    //    컴퓨터 개수
    private static int M;    //    회선 개수
    
    private static List<Edge>[] graph;        //    초기 그래프
    private static List<Integer>[] tree;      //    shortest-path-tree
    
    public static void main(String[] args) throws IOException {
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        
        graph = new ArrayList[N + 1];
        tree = new ArrayList[N + 1];
        for(int i = 0; i <= N; i++) {
            graph[i] = new ArrayList<>();
            tree[i] = new ArrayList<>();
        }
        
        for(int m = 0; m < M; m++) {
            st = new StringTokenizer(br.readLine());
            int v1 = Integer.parseInt(st.nextToken());
            int v2 = Integer.parseInt(st.nextToken());
            int c = Integer.parseInt(st.nextToken());
            
            graph[v1].add(new Edge(v2, c));
            graph[v2].add(new Edge(v1, c));
        }
        
        sb.append(N - 1).append("\n");
        dijkstra();
        dfs(ROOT);
        
        System.out.print(sb);
    }    //    main-end
    
    private static void dfs(int v) {
        for(int child : tree[v]) {
            sb.append(v).append(" ").append(child).append("\n");
            dfs(child);
        }
    }
    
    private static void dijkstra() {
        PriorityQueue<Node> pq = new PriorityQueue<>();
        int[] dist = new int[N + 1];
        Arrays.fill(dist, INF);
        
        dist[ROOT] = 0;
        pq.offer(new Node(0, ROOT, dist[ROOT]));
        
        while(!pq.isEmpty()) {
            Node cur = pq.poll();
            int cv1 = cur.vertex1;
            int cv2 = cur.vertex2;
            int cc = cur.cost;
            
            if(dist[cv2] < cc)    //    cv2까지의 최단거리가 이미 알려져 있는 경우
                continue;
            
            if(cv1 != 0)
                tree[cv1].add(cv2);    //    cv2의 이전 정점은 cv1임
            
            for(Edge next : graph[cv2]) {
                int nv2 = next.vertex;
                int nc = cc + next.cost;
                
                if(nc < dist[nv2]) {
                    dist[nv2] = nc;
                    pq.offer(new Node(cv2, nv2, nc));
                }
            }
        }
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
        public int vertex1;
        public int vertex2;
        public int cost;
        
        public Node(int vertex1, int vertex2, int cost) {
            this.vertex1 = vertex1;
            this.vertex2 = vertex2;
            this.cost = cost;
        }
        
        @Override
        public int compareTo(Node other) {
            return Integer.compare(this.cost, other.cost);
        }
    }
}    //    Main-class-end
```